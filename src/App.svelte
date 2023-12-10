<script lang="ts">

  import AIRPODS_MAX_IMG from './assets/acc/airpodsmax.png'
  import DARK_CIRCLE_IMG from './assets/acc/darkcircles.png'
  import MINOI_SUNGLASSES_IMG from './assets/acc/minoisunglasses.png'
  import GLASSES_IMG from './assets/acc/glasses.png'
  import COOKIE_IMG from './assets/acc/cookies.png'
  import COTTON_CANDY_IMG from './assets/acc/cottoncandy.png'
  import TOT_IMG from './assets/acc/tot.png'
  import FLUSHING from './assets/acc/flushing.png'
  import BECHELOR_CAP from './assets/acc/bachelorcap.png'


  import pen_icon from './assets/pen.png'
  import eraser_icon from './assets/eraser.png'
  import select_icon from './assets/select.png'

  import BODY from './assets/myChito/body.png'
  import BODY_ACC from './assets/myChito/body-acc.png'
  import ARM from './assets/myChito/arm.png'
  import ARM_ACC from './assets/myChito/arm-acc.png'
  import SKIN from './assets/myChito/skin.png'
  import PANTS from './assets/myChito/pants.png'
  import EYE_BROW from './assets/myChito/eyebrow.png'
  import SMALL_HAIR from './assets/myChito/small-hair.png'
  import BIG_HAIR from './assets/myChito/big-hair.png'
  import LINE from './assets/myChito/chito-line.png'

  import ColorPicker from 'svelte-awesome-color-picker';
  import {fabric} from "fabric";
  import { onMount } from 'svelte';

  let canvas;
  let inputImage: HTMLInputElement;
  let backgroundImage: HTMLInputElement;
  let hex = '#8a8a8a';
  let body : ChitoImage;
  let arm : ChitoImage;
  let bodyAcc : ChitoImage;
  let armAcc : ChitoImage;
  let pants : ChitoImage;
  let eyebrow : ChitoImage;
  let smallHair : ChitoImage;
  let bigHair : ChitoImage;
  let skin : ChitoImage; 

  // 과잠 컬러
  let bodyColor = '#233479';
  let armColor = '#ffffff';
  let bodyAccColor = '#ffffff';
  let armAccColor = '#233479';

  // 바지 컬러
  let pantsColor = '#ffffff';

  // 치토 컬러
  let skinColor = '#fbe9bd';
  let eyebrowColor = '#ffffff';
  let bighairColor = '#4364ad';
  let smallhairColor = '#6f9fd2';

  let isDrawing = false;
  let penColor = "#000000";  // 기본 펜 색상
  let penSize = 5;  // 기본 펜 크기

  let width = 600;
  let airpods_max : AccImage;
  let dark_circle : AccImage;
  let minoi_sunglasses : AccImage;
  let glasses : AccImage;
  let cookies : AccImage;
  let cotton_candy : AccImage;
  let uniform : AccImage;
  let bechelor_cap : AccImage;
  let flushing : AccImage;

   let currentToolbar = "default"; 
   const setToolbar = (toolbarName) => {
    currentToolbar = toolbarName;
    isDrawing = false;
    canvas.isDrawingMode = isDrawing;
  }
  class AccImage {
    url: string;
    isVisible: boolean;
    imageInstance: fabric.Image | null = null;  // fabric 이미지 객체를 참조하기 위한 변수

    constructor(url: string, isVisible = false) {
        this.url = url;
        this.isVisible = isVisible;
    }

    addToCanvas(canvas: fabric.Canvas) {
        fabric.Image.fromURL(this.url, (img) => {
        img.scaleToWidth(width);
            img.set({
                selectable: false,
                lockMovementX: true,
                lockMovementY: true,
                lockScalingX: true,
                lockScalingY: true,
                lockRotation: true,
                visible: this.isVisible , // 초기 visibility 설정
            });

            this.imageInstance = img;  // 참조 저장
            canvas.add(img);
        });
    }

    toggleVisibility() {
        if (this.imageInstance) {
            this.isVisible = !this.isVisible;
            this.imageInstance.visible = this.isVisible;
        }
    }
}

  class ChitoImage {
    url: string;
    isVisible: boolean;
    color: string;
    imageInstance: fabric.Image | null = null;  // fabric 이미지 객체를 참조하기 위한 변수

    constructor(url: string, color : string ,isVisible = true) {
        this.color = color
        this.url = url;
        this.isVisible = isVisible;
    }

    addToCanvas(canvas: fabric.Canvas): Promise<void> {
      return new Promise((resolve, reject) => {
          fabric.Image.fromURL(this.url, (img) => {
              if (!img) {
                  reject(new Error('Image loading failed'));
                  return;
              }

              img.scaleToWidth(width);
              img.set({
                  selectable: false,
                  lockMovementX: true,
                  lockMovementY: true,
                  lockScalingX: true,
                  lockScalingY: true,
                  lockRotation: true,
                  visible: this.isVisible, // 초기 visibility 설정
              });

              const filter = new fabric.Image.filters.BlendColor({
                  color: this.color,
                  // mode: 'multiply'
              });

              img.filters.push(filter);
              img.applyFilters();
              canvas.renderAll();

              this.imageInstance = img;  // 참조 저장
              canvas.add(img);

              resolve();  // 이미지 처리가 끝났을 때 Promise를 완료함
          });
      });
  }

    updateImageColor(color) {
      this.color=color
        if (this.imageInstance) {
            const filter = new fabric.Image.filters.BlendColor({
                color: color
            });

            this.imageInstance.filters = [filter];
            this.imageInstance.applyFilters();
            canvas.renderAll();
        }
    }
}
$: body && body.updateImageColor(bodyColor);
$: bodyAcc && bodyAcc.updateImageColor(bodyAccColor);
$: arm && arm.updateImageColor(armColor);
$: armAcc && armAcc.updateImageColor(armAccColor);
$: skin && skin.updateImageColor(skinColor);
$: pants && pants.updateImageColor(pantsColor);
$: smallHair && smallHair.updateImageColor(smallhairColor);
$: bigHair && bigHair.updateImageColor(bighairColor);
$: eyebrow && eyebrow.updateImageColor(eyebrowColor);

const toggleIconVisibility = (icon) => {
    icon.toggleVisibility();
    canvas.renderAll();
}

  const getWidth = () => {
    if(window.innerWidth < 600) return window.innerWidth;
    return 600;
  }

function toggleDrawing() {
    isDrawing = !isDrawing;
    canvas.isDrawingMode = isDrawing;
    updateBrush();
  }

  function draw() {
    isDrawing = true;
    canvas.isDrawingMode = isDrawing;
    updateBrush();
  }

  function notDraw() {
    isDrawing = false;
    canvas.isDrawingMode = isDrawing;
    updateBrush();
  }

  function updateBrush() {
    if (canvas) {
      canvas.freeDrawingBrush.color = penColor;
      canvas.freeDrawingBrush.width = penSize;
    }
  }

  onMount(async ()=>{
    canvas = new fabric.Canvas('canvas');
    width = getWidth();

    canvas.setWidth(width * canvas.getZoom());
    canvas.setHeight(width * canvas.getZoom());

    body = new ChitoImage(BODY,bodyColor);
    await body.addToCanvas(canvas)

    bigHair = new ChitoImage(BIG_HAIR,bighairColor);
    await bigHair.addToCanvas(canvas)

    skin = new ChitoImage(SKIN,skinColor);
    await skin.addToCanvas(canvas)

    arm = new ChitoImage(ARM,armColor);
    await arm.addToCanvas(canvas)

    bodyAcc = new ChitoImage(BODY_ACC,bodyAccColor);
    await bodyAcc.addToCanvas(canvas)

    armAcc = new ChitoImage(ARM_ACC,armAccColor);
    await armAcc.addToCanvas(canvas)

    pants = new ChitoImage(PANTS,pantsColor);
    await pants.addToCanvas(canvas)

    smallHair = new ChitoImage(SMALL_HAIR,smallhairColor);
    await smallHair.addToCanvas(canvas)

    eyebrow = new ChitoImage(EYE_BROW,eyebrowColor);
    await eyebrow.addToCanvas(canvas)
    

    fabric.Image.fromURL(LINE, function(img) {
      
      img.scaleToWidth(width);
      img.selectable = false;
      canvas.add(img)
      airpods_max = new AccImage(AIRPODS_MAX_IMG);
      airpods_max.addToCanvas(canvas);
      dark_circle = new AccImage(DARK_CIRCLE_IMG);
      dark_circle.addToCanvas(canvas);
      glasses = new AccImage(GLASSES_IMG);
      glasses.addToCanvas(canvas);
      minoi_sunglasses = new AccImage(MINOI_SUNGLASSES_IMG);
      minoi_sunglasses.addToCanvas(canvas);
      cookies = new AccImage(COOKIE_IMG);
      cookies.addToCanvas(canvas);
      uniform = new AccImage(TOT_IMG);
      uniform.addToCanvas(canvas);
      cotton_candy = new AccImage(COTTON_CANDY_IMG);
      cotton_candy.addToCanvas(canvas);
      bechelor_cap = new AccImage(BECHELOR_CAP);
      bechelor_cap.addToCanvas(canvas);
      flushing = new AccImage(FLUSHING);
      flushing.addToCanvas(canvas);

      canvas.renderAll()
    });

    canvas.isDrawingMode = isDrawing;
    updateBrush();
  })

  $: if(canvas){
      canvas.setBackgroundColor(hex)
      canvas.renderAll()
  }

  // 배경 이미지 설정 함수
  function setBackgroundImage(event) {
    const file = event.target.files[0];
    const reader = new FileReader();
    reader.onload = (e) => {
      fabric.Image.fromURL(e.target.result.toString(), (img) => {
        canvas.setBackgroundImage(img, canvas.renderAll.bind(canvas), {
          scaleX: canvas.width / img.width,
          scaleY: canvas.height / img.height
        });
      });
    };
    reader.readAsDataURL(file);
  }

  // 배경 이미지 제거 함수
function removeBackgroundImage() {
    canvas.setBackgroundImage(null, canvas.renderAll.bind(canvas));
    backgroundImage.value = "";  // 입력 요소의 값을 초기화
}

// 그림 그리기 기능을 활성화/비활성화하는 변수
  let isDrawingMode = false;

  const handleAddImage = (e: Event & {
    currentTarget: EventTarget & HTMLInputElement;
}) => {
    const files = e.currentTarget.files;
    if (!files) return;

    Array.from(files).forEach((file) => {
        const url = URL.createObjectURL(file);

        fabric.Image.fromURL(url, function (img) {
            const left = 235 / 600;
            const top = 360 / 600;
            img.set({
                left: (left * width) + (Math.random() - 0.5) * 100, // 위치를 약간 무작위로 조절하여 겹치지 않게 함
                top: (top * width) + (Math.random() - 0.5) * 100, // 위치를 약간 무작위로 조절하여 겹치지 않게 함
                angle: 0,
            });
            img.scaleToWidth(width / 6);
            canvas.add(img);
        });
    });
}


function removeImage() {
  const activeObjects = canvas.getActiveObjects();
  if (activeObjects.length) {
    activeObjects.forEach(object => {
      canvas.remove(object);
    });
    canvas.discardActiveObject().renderAll(); // 선택된 객체들을 취소하고 캔버스를 다시 렌더링
  }
  notDraw()
}
</script>

<main style={`width: ${width}`}>
  <h1>~나만의 치토 만들기~</h1>
  <canvas id="canvas" width="2400" height="2400" style="border:1px solid #ccc"></canvas>
  <div class="toolbar-selector">
    <button class="toggle-button" on:click={() => setToolbar("default")}>배경</button>
    <button class="toggle-button" on:click={() => setToolbar("icon")}>액세사리</button>
    <button class="toggle-button" on:click={() => setToolbar("clothesColor")}>의상</button>
    <button class="toggle-button" on:click={() => setToolbar("chitoColor")}>치토</button>
    <button class="toggle-button" on:click={() => setToolbar("draw")}>그리기</button>
    <button class="toggle-button" on:click={() => setToolbar("save")}>저장</button>
    
    <!-- 추가로 다른 툴바 버튼들을 이곳에 배치 -->
  </div>

  {#if currentToolbar === "default"}
  <div class="toolbar">
    <div>
      <h2>배경색</h2>
      <ColorPicker bind:hex isA11yClosable={false} label=''/>
    </div>
<div>
  <h2>배경 사진</h2>
  <div>
    <button on:click={() => backgroundImage.click()}>추가</button>
    <button on:click={removeBackgroundImage}>제거</button>
  </div>
  <input bind:this={backgroundImage} on:change={setBackgroundImage} type="file" accept="image/*" style="display: none" />
</div>
    </div>
     {/if}

    {#if currentToolbar === "clothesColor"}
    <div class="toolbar">
      <div>
        <h2>몸통</h2> 
        <ColorPicker bind:hex={bodyColor} isA11yClosable={false} label=''/>
      </div>
      <div>
        <h2>몸통 무늬</h2> 
        <ColorPicker bind:hex={bodyAccColor} isA11yClosable={false} label=''/>
      </div>
      <div>
        <h2>팔</h2> 
        <ColorPicker bind:hex={armColor} isA11yClosable={false} label=''/>
      </div>
      <div>
        <h2>팔 무늬</h2> 
        <ColorPicker bind:hex={armAccColor} isA11yClosable={false} label=''/>
      </div>
      <div>
        <h2>바지</h2> 
        <ColorPicker bind:hex={pantsColor} isA11yClosable={false} label=''/>
      </div>
    </div>
   {/if}

   {#if currentToolbar === "chitoColor"}
    <div class="toolbar">
      <div>
        <h2>피부</h2> 
        <ColorPicker bind:hex={skinColor} isA11yClosable={false} label=''/>
      </div>
      <div>
        <h2>머리 바깥쪽</h2> 
        <ColorPicker bind:hex={bighairColor} isA11yClosable={false} label=''/>
      </div>
      <div>
        <h2>머리 안쪽</h2> 
        <ColorPicker bind:hex={smallhairColor} isA11yClosable={false} label=''/>
      </div>
      <div>
        <h2>눈썹</h2> 
        <ColorPicker bind:hex={eyebrowColor} isA11yClosable={false} label=''/>
      </div>
    </div>
   {/if}

    {#if currentToolbar === "icon"}
  <div class="acctoolbar">
     <div class="vertical-buttons">
        <button on:click={()=>toggleIconVisibility(cotton_candy)}>
        {'솜사탕'}
        </button>
        <button on:click={()=>toggleIconVisibility(dark_circle)}>
        {'다크서클'}
        </button>
        <button on:click={()=>toggleIconVisibility(glasses)}>
        {'안경'}
        </button>
        <button on:click={()=>toggleIconVisibility(cookies)}>
        {'쿠키'}
        </button>
    </div>
     <div class="vertical-buttons">
        <button on:click={()=>toggleIconVisibility(airpods_max)}>
        {'에어팟 맥스'}
        </button>
        <button on:click={()=>toggleIconVisibility(minoi_sunglasses)}>
        {'미노이 선글라스'}
        </button>
        <button on:click={()=>toggleIconVisibility(uniform)}>
        {'축구 유니폼'}
        </button>
    </div>
        <div class="vertical-buttons">
        <button on:click={()=>toggleIconVisibility(bechelor_cap)}>
        {'학사모'}
        </button>
        <button on:click={()=>toggleIconVisibility(flushing)}>
        {'홍조'}
        </button>
    </div>
      <div class="vertical-buttons">
      <h2>사진 추가</h2>
      <div>
        <button on:click={() => inputImage.click()}>추가</button>
        <button on:click={removeImage}>제거</button>
      </div>
      <input bind:this={inputImage} on:change={handleAddImage} type="file" accept="image/*" style="display: none" />
    </div>
    </div>
   {/if}
    {#if currentToolbar === "draw"}
   <div class="drawtoolbar">
           <div class="drawsubtoolbar">
        <h6>펜을 선택해 그림을 그릴 수 있습니다. <br>지우고 싶은 것을 선택 후하고 지우개를 누르면 지워집니다.<br><br></h6>
       </div>
  <!-- <button on:click={toggleDrawing}>{isDrawing ? '그리기 중지' : '그리기 시작'}</button> -->
  <div  class='drawsubtoolbar'>
    <button on:click={notDraw} class="{isDrawing ? '' : 'selected'}">
    <img src={select_icon} alt="Draw"  class="icon-size"  />
  </button>
    <button on:click={draw} class="{isDrawing ? 'selected' : ''}">
    <img src={pen_icon} alt="Draw"  class="icon-size"  />
  </button>
  <button on:click={removeImage}><img src={eraser_icon} alt="Erase" class="icon-size" /></button>
  </div>
  <div  class='drawsubtoolbar'>
  <div>
    <h2 >펜 색상</h2>
    <input type="color" bind:value={penColor} on:change={updateBrush} id="penColor" />
  </div>
  <div>
    <h2 >펜 크기: {penSize}</h2>
    <input type="range" bind:value={penSize} min="1" max="50" on:input={updateBrush} id="penSize" />
  </div>
  </div>
</div>
{/if}
    {#if currentToolbar === "save"}
    <div class="drawtoolbar">

           <div class="savesubtoolbar">
      <button on:click={(e) => {
        const data = canvas.toDataURL({format: 'png', quality: 1, multiplier: 4});
        const link = document.createElement('a');
        link.download = 'my-chito.png';
        link.href = data;
        link.click();
      }}>저장</button>
    </div>
               <div class="drawsubtoolbar">
        <h6>'@ajou_doit'을 태그하여 인스타에 공유하면 추첨을 통해 배달의민족 상품권을 드립니다!</h6>
       </div>
       </div>
{/if}
</main>

<footer style={`width: ${width}`}>
  <span>
    아주대학교 2023-2 웹시설
  </span>
  <span>
    <a>카카아톡</a>
  </span>
</footer>

<style>
  h1{
    margin: 20px 0;
  }
  a {
    color: #0098fa;
    text-decoration: none;
  }
  main, footer{
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    color: rgb(56, 56, 56);
  }
  footer{
    flex-direction: row;
    gap: 10px;
    font-weight: 800;
    color: rgb(98, 98, 98)
  }
.acctoolbar, .toolbar {
    display: flex;
    justify-content: center;
    width: 100%;
    max-width: 600px;
    margin: 20px auto;
    padding: 20px 0;
    border: 1px solid #ccc;
    border-radius: 10px;
  }

  .drawtoolbar {
    justify-content: center;
    width: 100%;
    max-width: 600px;
    margin: 20px auto;
    padding: 20px 0;
    border: 1px solid #ccc;
    border-radius: 10px;
  }

  .drawsubtoolbar {
    display: flex;
    justify-content: center;
    width: 100%;
    max-width: 600px;
    margin-bottom: 5px;
  }
   .savesubtoolbar{
    display: flex;
    justify-content: center;
    width: 100%;
    max-width: 600px;
    margin-bottom: 30px;
  }

  .savesubtoolbar > button {
    margin-right: 1rem;
    margin-left: 1rem;
  }

  .toolbar > div, .drawsubtoolbar > div {
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
     gap: 10px;      
  }
  .acctoolbar {
    width: 100%;
    display: flex;
     flex-direction: column;
    justify-content: center;
    align-items: center;
     gap: 10px;      
  }

  button {
    padding: 5px 10px;
    border: 2px solid #ccc;
    border-radius: 4px;
    background-color: #fff;
    font-weight: 700;
    cursor: pointer;
  }
  
.toggle-button:hover {
    background-color: #357ABD;
}

  .toggle-button {
    padding: 10px 20px;
    border: none;
    border-radius: 20px;
    background-color: #4A90E2;
    color: #FFFFFF;
    transition: background-color 0.3s;
    cursor: pointer;
}
  /* 툴바 선택자 스타일 */
  .toolbar-selector {
    display: flex;
    gap: 10px;
    margin-top: 20px;
  }

  .selected {
    border: 2px solid blue;
    border-radius: 4px;
  }

  .icon-size {
    width: 30px;  /* 원하는 너비로 설정 */
    height: 30px; /* 원하는 높이로 설정 */
  }

  .vertical-buttons {
    display: flex;
    flex-direction: row; 
    gap: 10px; 
  }

  .drawsubtoolbar > button {
    margin-right: 10px;
    margin-left: 10px;
  }

  @media only screen and (max-width: 768px) {

  h2 {
    font-size: 18px; /* 원하는 크기로 조절 */
  }

  /* 아이콘 크기 줄이기 */
  .icon-size {
    width: 15px;
    height: 15px;
  }
  .toolbar-selector {
    gap: 0.3rem;
  }
  .toggle-button {
    padding: 0.2rem 0.5rem;
  }
   button {
    padding: 0.2rem 0.5rem;  /* 패딩 값을 조절하여 버튼의 크기를 줄임 */
    font-size: 12px;  /* 버튼 내부의 글자 크기 조절 */
  }
}
</style>
