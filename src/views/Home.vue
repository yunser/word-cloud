<template>
    <my-page title="首页" :page="page">
        <div class="layout-body">
            <div class="layout-content">
                <div class="canvas-container" id="canvas-container">
                    <canvas id="canvas" class="canvas"></canvas>
                    <div id="html-canvas" class="canvas hide"></div>
                </div>
            </div>
            <div class="layout-side">
                <div id="app">
                    <div class="tab-content">
                        <ul id="tool-tab" class="nav nav-tabs">
                            <div class="nav-item active"><a class="nav-link" href="#tab-list" data-toggle="tab">数据</a></div>
                            <div class="nav-item"><a class="nav-link" href="#tab-config" data-toggle="tab">配置</a></div>
                            <div class="nav-item"><a class="nav-link" href="#tab-dim" data-toggle="tab">大小</a></div>
                            <div class="nav-item"><a class="nav-link" href="#tab-mask" data-toggle="tab">遮罩图片</a></div>
                            <div class="nav-item"><a class="nav-link" href="#tab-help" data-toggle="tab">帮助</a></div>
                           <div class="nav-item"><a class="nav-link" href="#tab-webfont" data-toggle="tab">字体</a></div>
                        </ul>
                        <div class="btns">
                            <button class="btn btn-primary" @click="create">重新生成词云</button>
                            <a class="btn btn-default"  href="#" @click="download">下载图片</a>
                        </div>
                        <select class="example" v-model="example" @change="exampleChange">
                            <option value="love">爱的世界</option>
                            <option value="red-chamber" lang="zh-tw">红楼梦</option>
                            <option value="taiwan">台湾</option>
                        </select>
                        <div class="tab-content">
                            <div class="tab-pane active" id="tab-list">
                                <div class="form-horizontal">
                                    <div class="form-group">
                                       <label class="control-label col-sm-4">加权因子</label>
                                        <div class="col-sm-12">
                                            <textarea class="form-control" v-model="list" placeholder="请输入数据" rows="16"></textarea>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="tab-pane" id="tab-config">
                                <div class="form-horizontal">
                                    <div class="form-group">
                                        <label class="control-label col-sm-4">加权因子</label>
                                        <div class="col-sm-8">
                                            <input class="form-control" v-model.number="weightFactor">
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <label class="control-label col-sm-4">网格大小</label>
                                        <div class="col-sm-8">
                                            <input class="form-control" v-model.number="gridSize">
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <label class="control-label col-sm-4">背景颜色</label>
                                        <div class="col-sm-8">
                                            <input class="form-control" v-model="bgColor" type="color">
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <label class="control-label col-sm-4">颜色</label>
                                        <div class="col-sm-8">
                                            <input class="form-control" v-model="color" type="color">
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <label class="control-label col-sm-4">字体</label>
                                        <div class="col-sm-8">
                                            <select class="form-control" v-model="font">
                                                <option value="微软雅黑">微软雅黑</option>
                                            </select>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="tab-pane" id="tab-dim">
                                <div class="form-horizontal">
                                    <div class="form-group">
                                        <label class="control-label col-sm-4">宽度（px）</label>
                                        <div class="col-sm-8">
                                            <input class="form-control" v-model.number="width" type="number" min="1">
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <label class="control-label col-sm-4">高度（px）</label>
                                        <div class="col-sm-8">
                                            <input class="form-control" v-model.number="height" type="number" min="1">
                                        </div>
                                    </div>
                                    <div class="form-group">
                                        <label class="control-label col-sm-4">显示器像素密度(<span title="Dots per 'px' unit">dppx</span>)</label>
                                        <div class="col-sm-8">
                                            <input class="form-control" v-model.number="dppx" type="number" min="1">
                                            <span class="help-block">Adjust the weight, grid size, and canvas pixel size for high pixel density displays.</span>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="tab-pane" id="tab-mask">
                                <label for="config-mask">选择遮罩图片</label>
                                <input class="btn btn-default file-select" type="file" id="config-mask" @change="filechange($event)">
                                <button class="btn btn-default btn-remove" id="config-mask-clear" type="button" @click="clearImage">删除图片</button>
                                <ul class="mask-list">
                                    <li class="mask-item" v-for="mask in masks" @click="setImage(mask)">
                                        <img class="mask-img" :src="mask">
                                    </li>
                                </ul>
                            </div>
                            <div class="tab-pane" id="tab-help">
                                <article class="article">
                                    <p>数据左边是权重，右边是文字，之间空格分开。权重越大，表示文字越重要。</p>
                                    <p>你可以自己上传遮罩图片，图片最好背景颜色和图案对比明显。</p>
                                    <p>上传遮罩图片后，最好设置图片大小，保持长宽比和遮罩图片比例一致</p>
                                    <p>如果生成的效果不好，可以尝试修改参数配置。</p>
                                </article>
                            </div>
                            <div class="tab-pane" id="tab-webfont">
                                <label for="config-css">Extra Web Font CSS</label>
                                <input type="url" id="config-css" size="40" class="input-xxlarge" placeholder="https://fonts.googleapis.com/css?family=Libre+Baskerville:700" value="https://fonts.googleapis.com/css?family=Finger+Paint">
                                <span class="help-block">Find your favorite font on <a href="https://www.google.com/webfonts">Google Web Fonts</a>. Re-run if the font didn't load in time.</span>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </my-page>
</template>

<script>
    /* eslint-disable */
var examples = {
    'taiwan': {
        list: (function() {
            var names = ['台灣', '台湾', 'Taiwan', '臺灣'];

            var str = ['40 台灣'];
            var i = 20;
            while (--i) {
                names.forEach(function(name) {
                    str.push(i + ' ' + name);
                });
            }

            return str.join('\n');
        }()),
        gridSize: 4,
        option: '{\n' +
        '  weightFactor: 1,\n' +
        '  rotateRatio: 0.5,\n' +
        '  rotationSteps: 2\n,' +
        '  ellipticity: 1,\n' +
        '  shape: function(theta) {\n' +
        '    /' + '/ Function for simple shapes can be generated manually with http://timdream.org/wordcloud2.js/shape-generator.html.\n' +
        '    var max = 1026;\n' +
        '    var leng = [290,296,299,301,305,309,311,313,315,316,318,321,325,326,327,328,330,330,331,334,335,338,340,343,343,343,346,349,353,356,360,365,378,380,381,381,381,391,394,394,395,396,400,400,408,405,400,400,400,401,401,403,404,405,408,410,413,414,414,415,416,418,420,423,425,430,435,440,446,456,471,486,544,541,533,532,533,537,540,537,535,535,533,546,543,539,531,529,530,533,529,528,529,522,521,520,509,520,520,533,522,523,526,528,527,532,537,539,539,540,539,538,533,532,524,523,513,503,482,467,443,438,435,431,429,427,426,422,422,426,426,423,419,414,410,407,404,401,396,393,393,395,392,389,388,383,379,378,376,375,372,369,368,359,343,335,332,327,323,314,308,300,294,290,288,289,290,282,275,269,263,257,242,244,237,235,235,232,231,225,224,221,219,218,218,217,217,215,215,214,214,214,214,214,215,215,216,213,213,212,211,209,207,205,204,206,205,205,205,205,204,203,203,201,200,199,197,195,193,192,192,190,189,187,186,186,183,183,182,182,181,179,180,179,178,178,177,177,176,176,176,176,175,175,175,175,175,175,174,174,175,175,175,175,176,177,176,177,177,177,180,179,179,180,179,179,179,178,178,178,178,177,178,177,179,179,179,180,180,181,181,181,183,183,184,184,186,187,189,189,192,195,193,194,193,194,194,191,189,196,195,196,199,200,201,200,200,200,200,202,203,204,205,210,210,210,211,210,214,218,219,226,231,233,235,235,235,235,236,238,240,241,243,245,246,249,249,249,255,257,264,271,272,274,275,276,276,278,285,292,294,296,301,304,313,320,330,333,337,342,345,348,352,358,363,376,386,379,386,387,387,399,402,402,410,415,420,425,430,429,436,435,438,442,447,451,454,455,474,477,481,484,492,486,488,501,509,544,553,552,553,564,579,593,600,627,637,644,644,643,641,640,641,641,643,643,648,651,653,659,671,678,685,690,698,705,711,715,722,729,738,760,770,777,780,788,792,796,800,803,806,808,810,809,815,819,821,823,826,828,830,834,838,849,854,861,884,891,909,932,996,1026,1016,1011,1015,1018,999,987,827,806,779,754,734,727,700,690,686,682,677,675,672,668,665,664,658,641,614,610,609,609,608,596,591,583,577,576,570,561,553,547,539,531,526,525,524,519,513,502,484,480,478,470,464,458,453,450,448,448,445,441,435,431,423,420,411,408,405,398,388,385,385,385,383,379,372,370,369,368,366,367,371,370,367,365,345,343,342,340,336,334,331,329,326,323,323,322,321,321,319,318,315,313,312,309,308,307,306,305,304,303,303,302,302,300,299,299,297,296,294,292,291,290,289,290,291,291,289,289,285,285,286,287,287,288,288,288,288,288,289,288,287,279,275,273,272,272,272,274,274,274,275,275,277,281,284,285,286,286,286,283,280,279,279,280,281,283,284,288,291];\n\n' +
        '    return leng[(theta / (2 * Math.PI)) * leng.length | 0] / max;\n' +
        '  }\n'+
        '}'
    },
    'web-tech': {
        list: '26 Web Technologies\n20 HTML\n20 <canvas>\n' +
        '15 CSS\n15 JavaScript\n12 Document Object Model\n12 <audio>\n12 <video>\n12 Web Workers\n12 XMLHttpRequest\n12 SVG\n' +
        '9 JSON.parse()\n9 Geolocation\n9 data attribute\n9 transform\n9 transition\n9 animation\n' +
        '7 setTimeout\n7 @font-face\n7 Typed Arrays\n7 FileReader API\n7 FormData\n7 IndexedDB\n' +
        '7 getUserMedia()\n7 postMassage()\n7 CORS\n6 strict mode\n6 calc()\n6 supports()\n' +
        '6 media queries\n6 full screen\n6 notification\n6 orientation\n6 requestAnimationFrame\n' +
        '5 border-radius\n5 box-sizing\n5 rgba()\n5 text-shadow\n5 box-shadow\n5 flexbox\n5 viewpoint',
        gridSize: 18,
        option: '{\n' +
        '  weightFactor: 3,\n' +
        '  hover: window.drawBox,\n' +
        '  click: function(item) {\n' +
        '    alert(item[0] + \': \' + item[1]);\n' +
        '  },\n' +
        '}',
        fontCSS: 'https://fonts.googleapis.com/css?family=Finger+Paint'
    },
    'les-miz': {
        list: '30 Les Misérables\n20 Victor Hugo\n15 Jean Valjean\n15 Javert\n15 Fantine\n' +
        '15 Cosette\n12 Éponine\n12 Marius\n12 Enjolras\n10 Thénardiers\n10 Gavroche\n' +
        '10 Bishop Myriel\n10 Patron-Minette\n10 God\n8 ABC Café\n8 Paris\n8 Digne\n' +
        '8 Elephant of the Bastille\n5 silverware\n5 Bagne of Toulon\n5 loaf of bread\n' +
        '5 Rue Plumet\n5 revolution\n5 barricade\n4 sewers\n4 Fex urbis lex orbis',
        gridSize: 18,
        option: '{\n' +
        '  weightFactor: 3,\n' +
        '  color: function() {\n' +
        '    return ([\'#d0d0d0\', \'#e11\', \'#44f\'])[Math.floor(Math.random() * 3)]\n' +
        '  },\n' +
        '}',
        fontCSS: 'https://fonts.googleapis.com/css?family=Average'
    },
    'red-chamber': {
        list: '6 紅樓夢\n3 賈寶玉\n3 林黛玉\n3 薛寶釵\n3 王熙鳳\n3 李紈\n3 賈元春\n3 賈迎春\n' +
        '3 賈探春\n3 賈惜春\n3 秦可卿\n3 賈巧姐\n3 史湘雲\n3 妙玉\n2 賈政\n2 賈赦\n' +
        '2 賈璉\n2 賈珍\n2 賈環\n2 賈母\n2 王夫人\n2 薛姨媽\n2 尤氏\n2 平兒\n2 鴛鴦\n' +
        '2 襲人\n2 晴雯\n2 香菱\n2 紫鵑\n2 麝月\n2 小紅\n2 金釧\n2 甄士隱\n2 賈雨村',
        gridSize: 8,
        option: '{\n' +
        '  weightFactor: 16,\n' +
        '  rotateRatio: 0\n' +
        '}'
    },
    'love' : {
        list: (function generateLoveList() {
            var list = ['12 Love'];
            var nums = [5, 4, 3, 2, 2];
            // This list of the word "Love" in language of the world was taken from
            // the Language links of entry "Love" in English Wikipedia, with duplicate
            // spelling removed.
            var words = ('Liebe,ፍቅር,Lufu,حب,Aimor,Amor,Heyran,ভালোবাসা,Каханне,Любоў,Любов,བརྩེ་དུང་།,' +
            'Ljubav,Karantez,Юрату,Láska,Amore,Cariad,Kærlighed,Armastus,Αγάπη,Amo,Amol,Maitasun,' +
            'عشق,Pyar,Amour,Leafde,Gràdh,愛,爱,પ્રેમ,사랑,Սեր,Ihunanya,Cinta,ᑕᑯᑦᓱᒍᓱᑉᐳᖅ,Ást,אהבה,' +
            'ಪ್ರೀತಿ,სიყვარული,Махаббат,Pendo,Сүйүү,Mīlestība,Meilė,Leefde,Bolingo,Szerelem,' +
            'Љубов,സ്നേഹം,Imħabba,प्रेम,Ái,Хайр,အချစ်,Tlazohtiliztli,Liefde,माया,मतिना,' +
            'Kjærlighet,Kjærleik,ପ୍ରେମ,Sevgi,ਪਿਆਰ,پیار,Miłość,Leevde,Dragoste,' +
            'Khuyay,Любовь,Таптал,Dashuria,Amuri,ආදරය,Ljubezen,Jaceyl,خۆشەویستی,Љубав,Rakkaus,' +
            'Kärlek,Pag-ibig,காதல்,ప్రేమ,ความรัก,Ишқ,Aşk,محبت,Tình yêu,Higugma,ליבע').split(',');

            nums.forEach(function(n) {
                words.forEach(function(w) {
                    list.push(n + ' ' + w);
                });
            });

            return list.join('\n');
        })(),
        /*option: '{\n' +
        '  gridSize: Math.round(16 * $(\'#canvas\').width() / 1024),\n' +
        '  weightFactor: function (size) {\n' +
        '    return Math.pow(size, 2.3) * $(\'#canvas\').width() / 1024;\n' +
        '  },\n' +
        '  color: function (word, weight) {\n' +
        '    return (weight === 12) ? \'#f02222\' : \'#c09292\';\n' +
        '  },\n' +
        '  rotateRatio: 0.5,\n' +
        '  rotationSteps: 2,\n' +
        '}'*/
    }
}

    export default {
        data () {
            return {
                font: '微软雅黑',
                bgColor: '#ffffff',
                gridSize: 16,
                weightFactor: 8,
                example: '',
                text: '',
                option: '',
                list: '',
                color: '#0099cc',
                width: 560,
                height: 560,
                dppx: 1,
                masks: [
                    '/static/wordcloud/img/mask-heart.png',
                    '/static/wordcloud/img/mask-apple.png',

                    '/static/wordcloud/img/mask-rect.png',
                    '/static/wordcloud/img/mask-ellipse.png',

                    '/static/wordcloud/img/mask-china.png',
                    '/static/wordcloud/img/mask-us.png',

                    '/static/wordcloud/img/mask-qiaobusi.png',

                    '/static/wordcloud/img/mask-rabbit.png',
                    '/static/wordcloud/img/mask-fish.png',
                    '/static/wordcloud/img/mask-cat.png',
                    '/static/wordcloud/img/mask-bird.png',
                    '/static/wordcloud/img/mask-butterfly.png',

                    '/static/wordcloud/img/mask-girl.png',
                    '/static/wordcloud/img/mask-man.png',
                    '/static/wordcloud/img/mask-play.png',

                    '/static/wordcloud/img/mask-1.png',
                    '/static/wordcloud/img/mask-2.png',
                    '/static/wordcloud/img/mask-3.png',
                    '/static/wordcloud/img/mask-4.png',
                    '/static/wordcloud/img/mask-5.png',
                    '/static/wordcloud/img/mask-6.png',
                    '/static/wordcloud/img/mask-7.png',
                    '/static/wordcloud/img/mask-a.png',
                    '/static/wordcloud/img/mask-b.png',
                    '/static/wordcloud/img/mask-c.png',
                    '/static/wordcloud/img/mask-d.png',
                    '/static/wordcloud/img/mask-e.png',
                    '/static/wordcloud/img/mask-f.png',
                    '/static/wordcloud/img/mask-g.png',
                    '/static/wordcloud/img/mask-h.png',
                    '/static/wordcloud/img/mask-i.png',
                    '/static/wordcloud/img/mask-j.png',
                    '/static/wordcloud/img/mask-k.png',
                    '/static/wordcloud/img/mask-l.png',
                    '/static/wordcloud/img/mask-m.png',
                    '/static/wordcloud/img/mask-m.png',
                    '/static/wordcloud/img/mask-o.png',
                    '/static/wordcloud/img/mask-p.png',
                    '/static/wordcloud/img/mask-q.png',
                    '/static/wordcloud/img/mask-r.png',
                    '/static/wordcloud/img/mask-s.png',
                    '/static/wordcloud/img/mask-t.png',
                    '/static/wordcloud/img/mask-u.png',
                    '/static/wordcloud/img/mask-v.png',
                    '/static/wordcloud/img/mask-w.png',
                    '/static/wordcloud/img/mask-x.png',
                    '/static/wordcloud/img/mask-y.png',
                    '/static/wordcloud/img/mask-z.png'
                ],
                page: {
                    menu: [
                        {
                            type: 'icon',
                            icon: 'help',
                            to: '/help'
                        }
                    ]
                }
            }
        },
        mounted() {
            this.init()
        },
        methods: {
            clearImage: function () {
                this.maskCanvas = null
            },
            setImage(src) {
                var _this = this
                var img = new Image();
                img.src = src;
                img.onload = () => {
                    this.loadImage(img)
                    _this.run()
                }
            },
            filechange: function (e) {
                this.input=e.target
                if (!e.target.files.length) {
                    return
                }
                this.myfile = e.target.files[0]

                loadFile(this.myfile)
            },
            loadExampleData: function (name) {
                var example = examples[name];

                this.option = example.option || ''
                this.list = example.list || ''

                this.$css.val(example.fontCSS || '');
            },
            init: function () {
                

                this.$canvas = $('#canvas')
                this.$htmlCanvas = $('#html-canvas')
                var $canvasContainer = $('#canvas-container')

                var $mask = $('#config-mask')
                var $dppx = $('#config-dppx')
                this.$css = $('#config-css')
                this.$webfontLink = $('#link-webfont')

                if (!WordCloud.isSupported) {
                    // $('#not-supported').prop('hidden', false);
                    // $form.find('textarea, input, select, button').prop('disabled', true);
                    //return;
                }

                // function drawBox(item, dimension)

                // Update the default value if we are running in a hdppx device
                if (('devicePixelRatio' in window) &&
                    window.devicePixelRatio !== 1) {
                    $dppx.val(window.devicePixelRatio)
                }

                this.$canvas.on('wordcloudstop', function wordcloudstopped(evt) {

                })

                // Load the local image file, read it's pixels and transform it into a
                // black-and-white mask image on the canvas.
                $mask.on('change', function() {
                    this.maskCanvas = null

                    var file = $mask[0].files[0]

                    if (!file) {
                        return
                    }

                    loadFile(file)
                })

                function loadImage(img) {
                    this.maskCanvas = document.createElement('canvas')
                    this.maskCanvas.width = img.width
                    this.maskCanvas.height = img.height

                    var ctx = this.maskCanvas.getContext('2d')
                    ctx.drawImage(img, 0, 0, img.width, img.height)

                    var imageData = ctx.getImageData(
                        0, 0, this.maskCanvas.width, this.maskCanvas.height)
                    var newImageData = ctx.createImageData(imageData)

                    for (var i = 0; i < imageData.data.length; i += 4) {
                        var tone = imageData.data[i] +
                            imageData.data[i + 1] +
                            imageData.data[i + 2]
                        var alpha = imageData.data[i + 3]

                        if (alpha < 128 || tone > 128 * 3) {
                            // Area not to draw
                            newImageData.data[i] =
                                newImageData.data[i + 1] =
                                    newImageData.data[i + 2] = 255
                            newImageData.data[i + 3] = 0
                        } else {
                            // Area to draw
                            newImageData.data[i] =
                                newImageData.data[i + 1] =
                                    newImageData.data[i + 2] = 0
                            newImageData.data[i + 3] = 255
                        }
                    }

                    ctx.putImageData(newImageData, 0, 0)
                }
                this.loadImage = loadImage

                let _this = this
                function loadFile(file) {
                    var url = window.URL.createObjectURL(file)
                    var img = new Image()
                    img.src = url

                    img.onload = function () {
                        window.URL.revokeObjectURL(url)
                        _this.loadImage(img)
                    }
                }

                if ($mask[0].files.length) {
                    $mask.trigger('change')
                }
                this.loadExampleData('love')
                this.run()
            },
            create: function () {
                this.run()
            },
            exampleChange: function () {
                var name = this.example
                if (!name) {
                    this.run()
                } else if (name in examples) {
                    this.loadExampleData(name)
                    this.run()
                }
            },
            run: function () {
                // Load web font
                this.$webfontLink.prop('href', this.$css.val())

                // devicePixelRatio
                var devicePixelRatio = parseFloat(this.dppx)

                // Set the width and height
                var width = this.width || 560
                var height = this.height || 560
                var pixelWidth = width
                var pixelHeight = height

                if (devicePixelRatio !== 1) {
                    this.$canvas.css({'width': width + 'px', 'height': height + 'px'})

                    pixelWidth *= devicePixelRatio
                    pixelHeight *= devicePixelRatio
                } else {
                    this.$canvas.css({'width': '', 'height': '' })
                }

                this.$canvas.attr('width', pixelWidth)
                this.$canvas.attr('height', pixelHeight)

                this.$htmlCanvas.css({'width': pixelWidth + 'px', 'height': pixelHeight + 'px'})

                // Set the options object
                var options = {}

                var _this = this
                if (this.option) {
                    options = (function evalOptions() {
                        try {
                            return eval('(' + _this.option + ')')
                        } catch (error) {
                            alert('The following Javascript error occurred in the option definition; all option will be ignored: \n\n' +
                                error.toString())
                            return {}
                        }
                    })()
                }

                options.weightFactor = this.weightFactor
                options.gridSize = this.gridSize
                options.backgroundColor = this.bgColor

                // Set devicePixelRatio options
                if (devicePixelRatio !== 1) {
                    if (!('gridSize' in options)) {
                        options.gridSize = 8
                    }
                    options.gridSize *= devicePixelRatio

                    if (options.origin) {
                        if (typeof options.origin[0] == 'number')
                            options.origin[0] *= devicePixelRatio
                        if (typeof options.origin[1] == 'number')
                            options.origin[1] *= devicePixelRatio
                    }

                    if (!('weightFactor' in options)) {
                        options.weightFactor = 1
                    }
                    if (typeof options.weightFactor == 'function') {
                        var origWeightFactor = options.weightFactor
                        options.weightFactor =
                            function weightFactorDevicePixelRatioWrap() {
                                return origWeightFactor.apply(this, arguments) * devicePixelRatio
                            }
                    } else {
                        options.weightFactor *= devicePixelRatio
                    }
                }

                options.color = this.color

                // Put the word list into options
                if (this.list) {
                    var list = []
                    $.each(_this.list.split('\n'), function each(i, line) {
                        if (!$.trim(line))
                            return

                        var lineArr = line.split(' ')
                        var count = parseFloat(lineArr.shift()) || 0
                        list.push([lineArr.join(' '), count])
                    })
                    options.list = list
                }

                if (this.maskCanvas) {
                    options.clearCanvas = false

                    /* Determine bgPixel by creating
                    another canvas and fill the specified background color. */
                    var bctx = document.createElement('canvas').getContext('2d')

                    bctx.fillStyle = options.backgroundColor || '#fff'
                    bctx.fillRect(0, 0, 1, 1)
                    var bgPixel = bctx.getImageData(0, 0, 1, 1).data

                    this.maskCanvasScaled =
                        document.createElement('canvas')
                    this.maskCanvasScaled.width = this.$canvas[0].width
                    this.maskCanvasScaled.height = this.$canvas[0].height
                    var ctx = this.maskCanvasScaled.getContext('2d')

                    ctx.drawImage(this.maskCanvas,
                        0, 0, this.maskCanvas.width, this.maskCanvas.height,
                        0, 0, this.maskCanvasScaled.width, this.maskCanvasScaled.height)

                    var imageData = ctx.getImageData(0, 0, canvas.width, canvas.height)
                    var newImageData = ctx.createImageData(imageData)
                    for (var i = 0; i < imageData.data.length; i += 4) {
                        if (imageData.data[i + 3] > 128) {
                            newImageData.data[i] = bgPixel[0]
                            newImageData.data[i + 1] = bgPixel[1]
                            newImageData.data[i + 2] = bgPixel[2]
                            newImageData.data[i + 3] = bgPixel[3]
                        } else {
                            // This color must not be the same w/ the bgPixel.
                            newImageData.data[i] = bgPixel[0]
                            newImageData.data[i + 1] = bgPixel[1]
                            newImageData.data[i + 2] = bgPixel[2]
                            newImageData.data[i + 3] = bgPixel[3] ? (bgPixel[3] - 1) : 0
                        }
                    }

                    ctx.putImageData(newImageData, 0, 0)

                    ctx = this.$canvas[0].getContext('2d')
                    ctx.drawImage(this.maskCanvasScaled, 0, 0)

                    this.maskCanvasScaled = ctx = imageData = newImageData = bctx = bgPixel = undefined
                }

                // Always manually clean up the html output
                if (!options.clearCanvas) {
                    this.$htmlCanvas.empty()
                    this.$htmlCanvas.css('background-color', options.backgroundColor || '#fff')
                }

                WordCloud([this.$canvas[0], this.$htmlCanvas[0]], options)
            },
            download: function () {
                var canvas = document.getElementById("canvas")
                var ctx = canvas.getContext("2d")
                canvas.toBlob(function(blob) {
                    saveAs(blob, 'yunser.com-' + new Date().getTime() + '.png')
                })
            },
            watch: {
                example: function () {
                    var name = this.example
                    if (!name) {
                        this.run()
                    } else if (name in examples) {
                        this.loadExampleData(name)
                        this.run()
                    }
                }
            }
        }
    }
</script>

<style scoped>
</style>
