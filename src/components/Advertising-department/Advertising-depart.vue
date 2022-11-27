<template>
  <ion-slides
    pager="true"
    :options="slideOpts"
    :pagination="true"
    :modules="modules"
  >
    <ion-slide class="swiper-slide">
      <div class="shadow-bg">
        <div class="swiper-top">
          <a href="/entrySection" slot="fixed" horizontal="end">O'tkazish</a>
        </div>
        <div class="swiper-bottom">
          <h2 class="swiper-title">Mahsulotlarni soting va bonuslar ishlang</h2>
        </div>
        <ion-fab color="primary" class="ion-fab-first">
          <ion-fab-button class="ion-fab-button-right" @click="slideNext">
            <ion-icon :icon="arrowForwardCircleOutline"></ion-icon>
          </ion-fab-button>
        </ion-fab>
      </div>
    </ion-slide>
    <ion-slide class="swiper-slide">
      <div class="shadow-bg">
        <div class="swiper-top">
          <a href="/entrySection">O'tkazish</a>
        </div>
        <div class="swiper-bottom">
          <h2 class="swiper-title">Ishonchli mahsulotlar savdosi</h2>
        </div>
        <ion-fab class="ion-fab-second">
          <ion-fab-button @click="slidePrev">
            <ion-icon :icon="arrowBackCircleOutline"></ion-icon>
          </ion-fab-button>
          <ion-fab-button @click="slideNext">
            <ion-icon :icon="arrowForwardCircleOutline"></ion-icon>
          </ion-fab-button>
        </ion-fab>
      </div>
    </ion-slide>
    <ion-slide class="swiper-slide">
      <div class="shadow-bg">
        <div class="swiper-top">
          <a href="/entrySection">O'tkazish</a>
        </div>
        <div class="swiper-bottom">
          <h2 class="swiper-title">Istalgan joydan turib ishlash imkoniyat</h2>
        </div>
        <ion-fab class="ion-fab-third" slot="fixed" horizontal="center">
          <ion-fab-button @click="slidePrev">
            <ion-icon :icon="checkmarkCircleOutline"></ion-icon>
          </ion-fab-button>
        </ion-fab>
      </div>
    </ion-slide>
    <ion-pagination></ion-pagination>
  </ion-slides>
</template>
    
    
    <script>
import "swiper/css/pagination";
import { IonSlides, IonSlide, IonFab } from "@ionic/vue";
import { Pagination } from "swiper";
import "swiper/css";
import "@ionic/vue/css/ionic-swiper.css";
import {
  arrowBackOutline,
  arrowUpCircleOutline,
  arrowForwardCircleOutline,
  arrowBackCircleOutline,
  checkmarkCircleOutline,
} from "ionicons/icons";

export default {
  components: { IonSlides, IonSlide, IonFab },
  data() {
    return {
      arrowBackOutline,
      arrowUpCircleOutline,
      arrowForwardCircleOutline,
      arrowBackCircleOutline,
      checkmarkCircleOutline,
    };
  },

  setup() {
    const slidePrev = () => {
      document.querySelector("ion-slides").slidePrev();
    };
    const slideNext = () => {
      document.querySelector("ion-slides").slideNext();
    };

    const slideOpts = {
      initialSlide: 0,
      speed: 900,
      pagination: {
        el: ".swiper-pagination",
       
      },

      on: {
        beforeInit() {
          this.classNames.push(`${this.params.containerModifierClass}flip`);
          this.classNames.push(`${this.params.containerModifierClass}3d`);
          const overwriteParams = {
            slidesPerView: 1,
            slidesPerColumn: 1,
            slidesPerGroup: 1,
            watchSlidesProgress: true,
            spaceBetween: 0,
            virtualTranslate: true,
          };
          this.params = Object.assign(this.params, overwriteParams);
          this.originalParams = Object.assign(
            this.originalParams,
            overwriteParams
          );
        },
        setTranslate() {
          const { $, slides, rtlTranslate: rtl } = this;
          for (let i = 0; i < slides.length; i += 1) {
            const $slideEl = slides.eq(i);
            let progress = $slideEl[0].progress;
            if (this.params.flipEffect.limitRotation) {
              progress = Math.max(Math.min($slideEl[0].progress, 1), -1);
            }
            const offset$$1 = $slideEl[0].swiperSlideOffset;
            const rotate = -180 * progress;
            let rotateY = rotate;
            let rotateX = 0;
            let tx = -offset$$1;
            let ty = 0;
            if (!this.isHorizontal()) {
              ty = tx;
              tx = 0;
              rotateX = -rotateY;
              rotateY = 0;
            } else if (rtl) {
              rotateY = -rotateY;
            }

            $slideEl[0].style.zIndex =
              -Math.abs(Math.round(progress)) + slides.length;

            if (this.params.flipEffect.slideShadows) {
              // Set shadows
              let shadowBefore = this.isHorizontal()
                ? $slideEl.find(".swiper-slide-shadow-left")
                : $slideEl.find(".swiper-slide-shadow-top");
              let shadowAfter = this.isHorizontal()
                ? $slideEl.find(".swiper-slide-shadow-right")
                : $slideEl.find(".swiper-slide-shadow-bottom");
              if (shadowBefore.length === 0) {
                shadowBefore = this.$(
                  `<div class="swiper-slide-shadow-${
                    this.isHorizontal() ? "left" : "top"
                  }"></div>`
                );
                $slideEl.append(shadowBefore);
              }
              if (shadowAfter.length === 0) {
                shadowAfter = this.$(
                  `<div class="swiper-slide-shadow-${
                    this.isHorizontal() ? "right" : "bottom"
                  }"></div>`
                );
                $slideEl.append(shadowAfter);
              }
              if (shadowBefore.length)
                shadowBefore[0].style.opacity = Math.max(-progress, 0);
              if (shadowAfter.length)
                shadowAfter[0].style.opacity = Math.max(progress, 0);
            }
            $slideEl.transform(
              `translate3d(${tx}px, ${ty}px, 0px) rotateX(${rotateX}deg) rotateY(${rotateY}deg)`
            );
          }
        },
        setTransition(duration) {
          const { slides, activeIndex, $wrapperEl } = this;
          slides
            .transition(duration)
            .find(
              ".swiper-slide-shadow-top, .swiper-slide-shadow-right, .swiper-slide-shadow-bottom, .swiper-slide-shadow-left"
            )
            .transition(duration);
          if (this.params.virtualTranslate && duration !== 0) {
            let eventTriggered = false;
            slides.eq(activeIndex).transitionEnd(function onTransitionEnd() {
              if (eventTriggered) return;
              if (!this || this.destroyed) return;

              eventTriggered = true;
              this.animating = false;
              const triggerEvents = ["webkitTransitionEnd", "transitionend"];
              for (let i = 0; i < triggerEvents.length; i += 1) {
                $wrapperEl.trigger(triggerEvents[i]);
              }
            });
          }
        },
      },
    };

    return {
      slideOpts,
      slideNext,
      slidePrev,
      modules: [Pagination],
    };
  },
};
</script>
  
    <style scoped >
.swiper-slide {
  padding: 0px 26px 0 26px;
  display: flex;
  justify-content: center;
}
.shadow-bg {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: 100vh;
  width: 100vh;
  position: relative;
  background: linear-gradient(180deg, rgba(0, 0, 0, 0) 29.42%, #000000 85.83%);
  /* background: linear-gradient(180deg, rgba(255, 255, 255, 0) 29.42%, black 92%); */
}
.swiper-slide:nth-child(1) {
  background: url("@/assets/images/adveristing-bg-first.png") no-repeat;
  background-size: 100% 100%;
}
.swiper-slide:nth-child(2) {
  background: url("@/assets/images/adveristing-bg-second.png") no-repeat;
  background-size: 100% 100%;
}
.swiper-slide:nth-child(3) {
  background: url("@/assets/images/adveristing-bg-third.png") no-repeat;
  background-size: 100% 100%;
}

.swiper-pagination {
  position: absolute;
  text-align: center;
  transition: 0.3s opacity;
  transform: translate3d(0, 0, 0);
  z-index: 10;
}
.swiper-pagination > .swiper-pagination-bullets {
  bottom: 150px;
  left: 0;
  width: 100%;
}
.swiper-pagination-bullets {
  height: 15px !important;
  width: 15px !important;
}
.slides-md {
  --background: red;
}
.swiper-container-horizontal > .swiper-pagination-bullets {
  bottom: 160px;
  width: 100%;
}
.swiper-pagination-bullet {
  --width: 12px;
  --height: 12px;
}
.swiper-title {
  margin-bottom: 210px;
  opacity: 1;
  line-height: 30px;
  font-weight: 700;
  font-size: 29px;
  text-align: center;
  color: white;
  font-family: Arial, Helvetica, sans-serif;
}
.swiper-top {
  display: flex;
  width: 100vw;
  padding: 20px 17px;
  justify-content: flex-end;
}
.swiper-top > a {
  color: rgb(0, 0, 0);
  font-size: 18px;
}
ion-icon {
  font-size: 30px;
  color: white;
}
ion-fab {
  position: absolute;
  bottom: 90px;
}
.ion-fab-first {
  right: 26px;
}
.ion-fab-second {
  display: flex;
  width: 99vw;
  align-items: center;
  justify-content: space-between;
  /* gap: 60%; */
  padding: 0 26px;
  margin-left: 1px;
}
.swiper-bottom {
  padding: 0 30px;
}

ion-fab-button::part(native) {
  width: 60px;
  height: 60px;
  --background: #4353e6;
  border-radius: 15px;
  box-shadow: 0px 1px 2px 0px rgba(0, 0, 0, 0.3),
    0px 1px 3px 1px rgba(0, 0, 0, 0.15);
}
</style>