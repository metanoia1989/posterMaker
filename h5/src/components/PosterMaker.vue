<script setup>
    import { reactive, ref } from 'vue';
    import html2canvas from 'html2canvas';

    const cates = [
        "CULTURE 人类文化", 
        "UNIVERSE 宇宙太空",  
        "TECHNOLOGY 全球科技",    
        "NATURE 环境与自然",          
        "RELIGION 宗教信仰",          
        "ART 全球艺术",   
        "PEOPLE 人物",        
        "CONFLICT 冲突局势",      
        "BIG CITY 全球城市",      
    ];

    const errMsg = ref('');
    const width = ref(500)
    const outline = ref(true)

    const form = reactive({
        title: '',
        content: '',
        cate_index: 0,
        image: null,
    });

    async function pickFile(e) {
        if (!e.target.files || !e.target.files[0]) return;

        errMsg.value = '';

        const file = e.target.files[0]
        const reader = new FileReader()
        const fileMimeType = file?.type
        if (fileMimeType.indexOf('image') === -1) {
            errMsg.value = '请选择图片文件';
            return
        }
        reader.onload = (e) => {
            form.image = e.target.result
        }
        reader.readAsDataURL(file)
    }

    function generatePoster() {
        errMsg.value = ''; 
        if (!form.title) {
            errMsg.value = '请输入标题';
        }

        outline.value = false;
        const preview = document.getElementById('poster-preview')

        html2canvas(preview)
            .then(function (canvas) {
                const img = new Image()
                img.src = canvas.toDataURL()
                const link = document.createElement('a')
                link.download = form.title + '.png'
                link.href = img.src
                document.body.appendChild(link)
                link.click()
                document.body.removeChild(link)
            })
            .then(function () {
                outline.value = true;
            });
    }


</script>

<template>
  <div class="poster-maker flex flex-wrap container mx-auto mt-16">
    <div class="poster-form w-full md:w-1/2 p-4">
        <h2 class="text-2xl font-bold">请填写表单</h2>
          <div class="mt-8 max-w-md">
            <div class="grid grid-cols-1 gap-6">
              <label class="block">
                <span class="text-gray-700">海报宽度</span>
                <input
                  type="number"
                  class="
                    mt-0
                    block
                    w-full
                    px-0.5
                    border-0 border-b-2 border-gray-200
                    focus:ring-0 focus:border-black
                  "
                  placeholder=""
                  v-model="width"
                />
                <p class="text-gray-500 text-xs italic mt-1">单位px, 默认为500px</p>
              </label>

              <label class="block">
                <span class="text-gray-700">新闻标题</span>
                <input
                  type="text"
                  class="
                    mt-0
                    block
                    w-full
                    px-0.5
                    border-0 border-b-2 border-gray-200
                    focus:ring-0 focus:border-black
                  "
                  placeholder=""
                  v-model="form.title"
                />
              </label>
              <label class="block">
                <span class="text-gray-700">新闻图片</span>
                <input
                  type="file"
                  class="
                    mt-0
                    block
                    w-full
                    px-0.5
                    border-0 border-b-0 border-gray-200
                    pt-2 
                    focus:ring-0 focus:border-black
                  "
                  @change="pickFile"
                  placeholder=""
                />
              </label>
              <label class="block">
                <span class="text-gray-700">新闻类型</span>
                <select
                  class="
                    block
                    w-full
                    mt-0
                    px-0.5
                    border-0 border-b-2 border-gray-200
                    focus:ring-0 focus:border-black
                  "
                  v-model="form.cate_index"
                >

                  <option v-for="(item, i) in cates" 
                    v-bind:key="i" 
                    :selected="form.cate_index == i"
                    :value="i"
                >
                    {{ item }}
                </option>
                </select>
              </label>
              <label class="block">
                <span class="text-gray-700">新闻内容</span>
                <textarea
                  class="
                    mt-0
                    block
                    w-full
                    px-0.5
                    border-0 border-b-2 border-gray-200
                    focus:ring-0 focus:border-black
                  "
                  rows="5"
                  v-model="form.content"
                ></textarea>
                <p class="text-red-500 text-xs italic mt-1">{{ errMsg }}</p>
              </label>
            </div>
            <div class="flex items-center justify-between mt-8">
                <button class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline" 
                    type="button" @click="generatePoster">
                    生成海报    
                </button>
            </div>
          </div>
    </div>

    <div class="preview-container w-full md:w-1/2 ">
        <div :class="['poster-preview', outline ? 'outline' :'']" id="poster-preview" :style="{ width: width + 'px' }">
            <img class="img-preview" v-if="form.image" :src="form.image" />
            <div class="news-content p-5">
                <div class="title-line">
                    <h2 class="title">{{ form.title }}</h2>
                    <p class="cate">{{ cates[form.cate_index] }}</p>
                </div>
                <p class="content">{{ form.content }}</p>
            </div>
        </div>
    </div>

  </div>
</template>

<style scoped>
    .preview-container {
        margin-top: 20px;
        margin-bottom: 20px;
    }
    .poster-preview, .news-content {
        display: flex;
        flex-direction: column;
    }
    .outline {
        outline: 1px solid gray;
    }

    .img-preview {
        width: 100%;
    }

    .title-line {
        display: flex;
        padding: 0px 0 1rem;
        justify-content: space-between;
        align-items: center;
    }

    .title {
        font-size: 1.6rem;
        font-weight: bold;
        max-width: calc(100% - 10rem) ;
        word-wrap: break-word;

    }
    .cate {
        /* flex: 1 0 auto; */
        /* max-width: 10.5rem; */
        height: 1.5rem;
        border: 1px grey solid;
        border-radius: 0.25rem;
        color: gray;
        padding: 0px 0.5rem;
        font-size: 0.9rem;
        white-space: nowrap;
        margin-left: 0.5rem;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .content {
        word-wrap: break-word;
    }
</style>
