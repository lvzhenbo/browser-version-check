<script setup lang="ts">
import { Github } from "@vicons/fa";
interface UA {
  architecture: string;
  bitness: string;
  brands: {
    brand: string;
    version: string;
  }[];
  fullVersionList: {
    brand: string;
    version: string;
  }[];
  mobile: boolean;
  model: string;
  platform: string;
  platformVersion: string;
}

const browserList = ["Google Chrome", "Microsoft Edge", "OperaMobie"];
const kernelList = ["Chromium", "Opera"];

const isSupport = ref(true);
const result = reactive({
  browser: "",
  browserVersion: "",
  kernel: "",
  kernelVersion: "",
  platform: "",
  platformVersion: "",
  architecture: "",
  bitness: "",
  mobile: false,
  model: "",
});
const temp = ref<UA>();

// @ts-ignore
if (navigator.userAgentData) {
  // @ts-ignore
  navigator.userAgentData
    .getHighEntropyValues([
      "architecture", // 平台架构
      "bitness", // 体系结构位数
      "model", // 移动设备型号
      "platformVersion", // 平台版本
      // "uaFullVersion", // uaFullVersion已被弃用由fullVersionList代替
      "fullVersionList", // 浏览器名称和完整版本
    ])
    .then((ua: UA) => {
      console.log(ua);

      ua.fullVersionList.forEach((item) => {
        if (browserList.includes(item.brand)) {
          result.browser = item.brand;
          result.browserVersion = item.version;
        }
        if (kernelList.includes(item.brand)) {
          result.kernel = item.brand;
          result.kernelVersion = item.version;
        }
      });
      // result.browser = ua.fullVersionList[2].brand;
      // result.browserVersion = ua.fullVersionList[2].version;
      // result.kernel = ua.fullVersionList[0].brand;
      // result.kernelVersion = ua.fullVersionList[0].version;
      result.platform = ua.platform;
      result.platformVersion = ua.platformVersion;
      result.architecture = ua.architecture;
      result.bitness = ua.bitness;
      result.mobile = ua.mobile;
      result.model = ua.model;
      temp.value = ua;
    });
} else {
  isSupport.value = false;
}
</script>

<template>
  <n-layout class="min-h-screen">
    <n-layout-header class="border-b">
      <n-grid class="my-4" item-responsive responsive="screen">
        <n-gi span="24 m:18 l:16" offset="0 m:2 l:4" class="mx-4">
          <div class="flex justify-between">
            <div class="text-xl font-bold">浏览器版本检测</div>
            <div class="flex items-center">
              <n-button
                text
                class="text-xl"
                tag="a"
                href="https://github.com/lvzhenbo/browser-version-check"
                target="_blank"
              >
                <n-icon>
                  <Github />
                </n-icon>
              </n-button>
            </div>
          </div>
        </n-gi>
      </n-grid>
    </n-layout-header>
    <n-layout-content>
      <n-grid class="my-4" item-responsive responsive="screen">
        <n-gi span="24 m:18 l:12" offset="0 m:2 l:6">
          <n-card hoverable>
            <div v-if="!isSupport">
              对不起，该浏览器或版本不支持
              <code>Navigator.userAgentData</code>
              API，请查看
              <n-button
                text
                tag="a"
                href="https://developer.mozilla.org/en-US/docs/Web/API/Navigator/userAgentData#browser_compatibility"
                target="_blank"
                type="primary"
              >
                MDN文档
              </n-button>
              所说明支持的浏览器和版本
            </div>
            <n-descriptions
              label-placement="left"
              bordered
              :column="2"
              v-if="isSupport"
            >
              <n-descriptions-item label="浏览器">
                {{ result.browser }}
              </n-descriptions-item>
              <n-descriptions-item label="浏览器版本">
                {{ result.browserVersion }}
              </n-descriptions-item>
              <n-descriptions-item label="内核">
                {{ result.kernel }}
              </n-descriptions-item>
              <n-descriptions-item label="内核版本">
                {{ result.kernelVersion }}
              </n-descriptions-item>
              <n-descriptions-item label="平台系统">
                {{ result.platform }}
              </n-descriptions-item>
              <n-descriptions-item label="系统版本">
                {{ result.platformVersion }}
              </n-descriptions-item>
              <n-descriptions-item label="平台架构">
                {{ result.architecture }}
              </n-descriptions-item>
              <n-descriptions-item label="体系结构位数">
                {{ result.bitness }}
              </n-descriptions-item>
              <n-descriptions-item label="是否移动设备">
                {{ result.mobile ? "是" : "否" }}
              </n-descriptions-item>
              <n-descriptions-item label="移动设备型号" v-if="result.mobile">
                {{ result.model }}
              </n-descriptions-item>
            </n-descriptions>
          </n-card>
        </n-gi>
      </n-grid>
      <!-- <div>
        {{ temp }}
      </div> -->
    </n-layout-content>
    <!-- <n-layout-footer position="absolute" class="inset-x-0 bottom-0">
      成府路
    </n-layout-footer> -->
  </n-layout>
</template>

<style scoped></style>
