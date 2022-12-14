<template>
  <van-config-provider :theme-vars="themeVars" style="width: 100%; height: 100%">
    <div class="home" ref="homeRef">
      <div class="home-content" v-if="newsData.length">
        <header>
          <img src="https://i.postimg.cc/fLy21sW8/header-bg.png" />
          <h1 class="title">始终坚持一切为了人民</h1>
          <p>——习近平</p>
        </header>
        <div class="bgc">
          <div class="left-bg"></div>
          <div class="md"></div>
          <div class="right-bg"></div>
        </div>
        <section class="news-wrapper">
          <ul>
            <van-list v-model:loading="loading" :finished="finished" @load="onLoad" :immediate-check="false" offset="50">
              <li v-for="(item, i) in newsData[0].page" :key="item.id" @click="getNewsInfo(item.id)" :class="{ 'last-li': i == newsData[0].page.length - 1 }">
                <div>
                  <article>
                    <section class="s-left">
                      <h4>{{ item.title }}</h4>
                      <div class="s-info">
                        <span class="s-source"> {{ item.source }} </span>
                        <span class="add-time">{{ "添加时间:" + item.date }}</span>
                      </div>
                    </section>
                    <section class="s-right">
                      <img v-lazy="item.small_img" />
                    </section>
                  </article>
                </div>
              </li>
            </van-list>
          </ul>

          <van-divider :style="{ color: '#cccccc', borderColor: '#cccccc' }" v-show="finished">END</van-divider>
        </section>
        <section class="fixIcon" :class="{ show: scrollVal >= 500 }">
          <a href="javascript:" class="fixTop-ico" @click="scrollTo(0)"></a>
          <a href="javascript:" class="fixBottom-ico" @click="scrollTo(1)"></a>
        </section>
      </div>
      <div class="loading" style="width: 100%; height: 100%" v-if="!newsData.length && !errStatus">
        <van-loading type="spinner" vertical>加载中...</van-loading>
      </div>
      <div class="err-box" v-if="errStatus">
        <van-empty image="error" description="请求失败，请刷新网页重试！" />
      </div>
      <div class="footer" v-if="newsData.length && !errStatus">
        <div class="footer-content">
          <p>
            <span>献给那些</span>
            <span>死于过度防疫的</span>
            <span>人们</span>
          </p>
        </div>
      </div>
    </div>
  </van-config-provider>
</template>

<script setup>
import { ref, onMounted, onActivated } from "vue";
import { useRouter } from "vue-router";
// 获取分页数据
import { getPageData } from "../getData";

const router = useRouter();
const { newsData, errStatus } = getPageData();
const homeRef = ref(null);
const scrollVal = ref(0);
const loading = ref(false);
const finished = ref(false);
// 滚动时，保存最新的scrollTop值
onMounted(() => {
  homeRef.value.addEventListener("scroll", (e) => {
    scrollVal.value = e.target.scrollTop;
  });
});

// 缓冲组件激活时触发这个钩子
onActivated(() => (homeRef.value.scrollTop = scrollVal.value));

const scrollTo = (value) => {
  if (value == 0) {
    scrollVal.value = value;
  } else {
    const scrollBottomVal = homeRef.value.scrollHeight - homeRef.value.clientHeight;
    scrollVal.value = scrollBottomVal;
  }
  homeRef.value.scrollTo({ top: scrollVal.value, behavior: "smooth" });
};

const getNewsInfo = (id) => {
  router.push({ path: `/news_info/${id}` });
};

let pageNum = ref(0);
// 滑动到页面底部执行
const onLoad = () => {
  // 模拟分页加载数据
  setTimeout(() => {
    pageNum.value += 1;
    if (pageNum.value < newsData.value.length) {
      newsData.value[0].page.push(...newsData.value[pageNum.value].page);
      // 加载状态结束
      loading.value = false;
      return;
    }
    // 数据全部加载完成
    finished.value = true;
  }, 800);
};

const themeVars = {
  dividerMargin: "10px 0",
};
</script>
<style lang="less" scoped>
.home {
  width: 100%;
  height: 100%;
  overflow-y: scroll;
  //   chrome去除滚动条样式
  &::-webkit-scrollbar {
    display: none;
  }
  //   兼容火狐
  &.scw {
    scrollbar-width: none;
    overflow: -moz-scrollbars-none;
  }
  //   兼容IE10+
  &.msscw {
    -ms-overflow-style: none;
  }
  .fixIcon {
    position: fixed;
    bottom: 238px;
    right: 13px;
    z-index: 999;
    display: none;

    &.show {
      display: block;
    }

    .fixTop-ico {
      display: block;
      width: 36px;
      height: 36px;
      background: url(https://x2.ifengimg.com/fe/shank/channel/top.e15cad56.png) no-repeat 0 0/36px 36px;
      margin-bottom: 6px;
    }

    .fixBottom-ico {
      .fixTop-ico();
      background: url(https://x2.ifengimg.com/fe/shank/channel/bottom.93b36099.png) no-repeat 0 0/36px 36px;
      margin-bottom: 0;
    }
  }

  header {
    width: 375px;
    height: 54px;
    position: relative;

    img {
      width: 100%;
      height: 100%;
    }

    .title {
      position: absolute;
      top: 0;
      padding: 16px 22px 0 22px;
      font-size: 17px;
      color: rgb(253, 253, 253);
      font-weight: 600;
    }

    p {
      position: absolute;
      right: 22px;
      color: white;
      bottom: 0;
      font-size: 14px;
      padding-bottom: 5px;
    }
  }

  .home-content {
    .bgc {
      width: 100%;
      height: 54px;
      display: flex;
      margin-top: -1px;

      .left-bg {
        width: 5%;
        height: 54px;
        background: linear-gradient(rgb(253, 55, 38), hsla(0, 0%, 100%, 0));
      }

      .md {
        flex: auto;
        height: 54px;
        background: linear-gradient(rgb(254, 100, 86), hsla(0, 0%, 100%, 0));
      }

      .right-bg {
        width: 5%;
        height: 54px;
        background: linear-gradient(rgb(255, 98, 54), hsla(0, 0%, 100%, 0));
      }
    }

    .news-wrapper {
      margin-top: -54px;
      position: relative;
      z-index: 5;

      ul {
        list-style: none;

        li {
          padding: 12px;
          margin: 10px;
          margin-top: 0;
          border-radius: 10px;
          background-color: rgb(255, 255, 255);
          &.last-li {
            margin-bottom: 0;
          }
          div {
            article {
              display: flex;
              .s-left {
                display: flex;
                flex-wrap: wrap;
                flex: 1;
                font-size: 15px;
                color: black;

                h4 {
                  display: -webkit-box;
                  -webkit-line-clamp: 2;
                  -webkit-box-orient: vertical;
                  flex: 100%;
                  max-height: 50px;
                  font-weight: 400;
                  overflow: hidden;
                  text-overflow: ellipsis;
                  line-height: 25px;
                  margin-top: -4px;
                }

                .s-info {
                  display: flex;
                  width: 100%;
                  justify-content: space-between;
                  align-items: end;
                  color: #999;
                  font-size: 12px;

                  .s-source {
                    max-width: 86px;
                    overflow: hidden;
                    text-overflow: ellipsis;
                    white-space: nowrap;
                  }
                }
              }

              .s-right {
                width: 108px;
                height: 84px;
                margin-left: 10px;

                img {
                  width: 100%;
                  height: 100%;
                  object-fit: cover;
                  border-radius: 3px;
                }

                img[lazy="loading"] {
                  background: linear-gradient(90deg, #f2f2f2 25%, #e6e6e6 37%, #f2f2f2 63%);
                  background-size: 400% 100%;
                  animation: loading-animate 1s ease infinite;
                }

                @keyframes loading-animate {
                  0% {
                    background-position: 100% 50%;
                  }

                  to {
                    background-position: 0 50%;
                  }
                }
              }
            }
          }
        }
      }
    }
  }
  .loading {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .err-box {
    display: flex;
    align-items: center;
    justify-content: center;
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
  }
  .footer {
    width: 100%;
    height: 238px;
    padding: 20px;
    background: url(./jesus.png) no-repeat 0 0/100% 100%;
    background-color: #0e163c;

    .footer-content {
      display: flex;
      height: 100%;
      align-items: center;
      p {
        display: flex;
        flex: 100%;
        font-size: 16px;
        color: #8f8f9f;
        font-family: "ZCOOL XiaoWei", serif;
        span {
          width: 16px;
        }
        :nth-child(2) {
          margin: 32px 8px 0 8px;
        }
        :nth-child(3) {
          color: #fff;
          padding-top: 138px;
        }
      }
    }
  }
}
</style>
