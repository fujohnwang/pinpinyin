<script>
    import {pinyin} from 'pinyin-pro';

    let zhValue = '';
    let pinyinValue = '';
    let hanziEmpty = true;
    let hidden = "hidden";
    let excerptValue = '';
    let baikeLink = '';

    function onEnter(e) {
        if (e.charCode === 13) {
            pinpinyin()
        }
    }

    function pinpinyin() {
        pinyinValue = pinyin(zhValue)
        if (pinyinValue == zhValue) {
            fetch(`https://corsproxy.afoo.me/corsproxy/?target=https://www.zdic.net/hans/${encodeURI(zhValue)}`).then(function (response) {
                return response.text();
            }).then(function (html) {
                try {
                    let parser = new DOMParser();
                    let doc = parser.parseFromString(html, 'text/html');
                    pinyinValue = doc.querySelector(".z_py .z_d.song").innerHTML
                } catch (error) {
                    console.warn(error)
                    pinyinValue = 'oops... 好像俺这里找不到这个字的音标😅'
                }
            }).catch(function (err) {
                // There was an error
                console.warn('Something went wrong.', err);
                pinyinValue = 'oops... 好像俺这里找不到这个字的音标😅'
            });
        }
	baikeLink = `https://baike.baidu.com/item/${encodeURI(zhValue)}`
//	fetch(`https://pa.afoo.me?url=${baikeLink}`).then(r=>r.json()).then(json=>excerptValue=json.page?.excerpt ).catch(err=>console.warn(err.message))
	
    }

    $: {
        hanziEmpty = zhValue == '';
        if (hanziEmpty) {
            pinyinValue = '';
	    excerptValue = '';
	    baikeLink = '';
        }
        if (pinyinValue == '') {
            hidden = 'hidden';
        } else {
            hidden = '';
        }
    }

</script>


<div class="lg:w-4/5 sm:mx-auto sm:mb-2">
    <div class="relative flex flex-col items-center">
        <label for="hanzi"
               class="leading-7 text-sm font-black text-gray-600 self-start">输入汉字(按回车键）获取拼音：</label>
        <input type="search" id="hanzi" name="hanzi" placeholder="这里输入汉字...(按Escape键清除)"
               class="w-full bg-gray-100 bg-opacity-50 rounded border border-gray-300 focus:border-indigo-500 focus:bg-white focus:ring-2 focus:ring-indigo-200 text-base outline-none text-gray-700 py-1 px-3 leading-8 transition-colors duration-200 ease-in-out"
               bind:value={zhValue} on:keypress={onEnter}
        >
        <button class="w-full my-1 text-white bg-indigo-500 border-0 py-2 px-6 focus:outline-none hover:bg-indigo-600 rounded text-lg disabled:bg-gray-500 disabled:text-slate-400 disabled:border-slate-200"
                on:click={pinpinyin} disabled={hanziEmpty}>
            给老子拼 🤪
        </button>

        <div class="pt-4 w-full h-full bg-gray-50 mt-3 p-8 rounded {hidden}">
            <svg xmlns="http://www.w3.org/2000/svg" fill="currentColor" class="block w-5 h-5 text-gray-400 mb-4"
                 viewBox="0 0 975.036 975.036">
                <path d="M925.036 57.197h-304c-27.6 0-50 22.4-50 50v304c0 27.601 22.4 50 50 50h145.5c-1.9 79.601-20.4 143.3-55.4 191.2-27.6 37.8-69.399 69.1-125.3 93.8-25.7 11.3-36.8 41.7-24.8 67.101l36 76c11.6 24.399 40.3 35.1 65.1 24.399 66.2-28.6 122.101-64.8 167.7-108.8 55.601-53.7 93.7-114.3 114.3-181.9 20.601-67.6 30.9-159.8 30.9-276.8v-239c0-27.599-22.401-50-50-50zM106.036 913.497c65.4-28.5 121-64.699 166.9-108.6 56.1-53.7 94.4-114.1 115-181.2 20.6-67.1 30.899-159.6 30.899-277.5v-239c0-27.6-22.399-50-50-50h-304c-27.6 0-50 22.4-50 50v304c0 27.601 22.4 50 50 50h145.5c-1.9 79.601-20.4 143.3-55.4 191.2-27.6 37.8-69.4 69.1-125.3 93.8-25.7 11.3-36.8 41.7-24.8 67.101l35.9 75.8c11.601 24.399 40.501 35.2 65.301 24.399z"></path>
            </svg>
            <p class="leading-relaxed mb-6 text-3xl">{pinyinValue}</p>
<!--	    <p class="leading-relaxed mb-6 text-3xl">{excerptValue}</p> -->
	    <p><a href="{baikeLink}">了解更多...</a></p>
        </div>
    </div>
</div>




