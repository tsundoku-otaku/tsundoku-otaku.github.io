<script setup lang="ts">
defineProps<{ title: string, description?: string, dir?: string }>()
</script>

<template>
  <div
    tw="w-full h-full bg-white flex flex-col"
    style="background:linear-gradient(0deg, rgba(255, 255, 255, 0.92), rgba(231,230,255,0.86)), url(https://tsundoku-otaku.github.io/img/open-graph-background.png)"
  >
    <div tw="p-10 w-full min-h-0 grow flex flex-col items-center justify-between">
      <div tw="w-full flex justify-between items-center text-4xl font-medium">
        <div tw="flex items-center">
          <svg width="64" height="64" viewBox="0 0 158 158" preserveAspectRatio="xMidYMid meet" fill="#114cd4" xmlns="http://www.w3.org/2000/svg"> <path id="path" d="M 15.64 147.496 L 15.64 10.51 L 142.356 10.51 L 142.356 146.232 L 130.506 146.232 L 130.506 21.57 L 27.174 21.57 L 27.174 147.496 L 15.64 147.496 Z M 72.046 104.678 L 72.046 26.468 L 82.316 26.468 L 82.316 105.942 Q 82.316 108.786 82.869 110.682 Q 83.422 112.578 84.686 113.21 Q 86.898 113.842 90.69 113.842 L 96.141 113.842 L 102.224 113.842 Q 104.278 113.842 106.49 113.763 Q 108.702 113.684 109.65 113.526 Q 110.598 113.21 111.783 112.499 Q 112.968 111.788 113.442 110.682 Q 113.758 109.576 114.074 106.732 Q 114.39 103.888 114.548 100.254 Q 116.286 101.676 118.814 102.861 Q 121.342 104.046 123.87 104.836 Q 123.554 108.47 123.001 112.183 Q 122.448 115.896 121.342 117.634 Q 120.394 119.372 118.735 120.478 Q 117.076 121.584 114.706 122.058 Q 112.652 122.532 109.334 122.848 Q 106.016 123.164 103.014 123.164 L 98.906 123.164 L 93.297 123.164 L 89.11 123.164 Q 86.108 123.164 82.711 122.532 Q 79.314 121.9 76.944 120.636 Q 74.416 119.214 73.231 116.133 Q 72.046 113.052 72.046 104.678 Z M 21.17 140.07 L 21.17 128.852 L 137.142 128.852 L 137.142 140.07 L 21.17 140.07 Z M 110.914 32.946 L 117.55 41.478 Q 109.966 43.216 100.407 44.717 Q 90.848 46.218 80.183 47.245 Q 69.518 48.272 58.695 48.983 Q 47.872 49.694 37.918 50.01 Q 37.76 48.272 36.97 45.823 Q 36.18 43.374 35.232 41.478 Q 45.028 41.004 55.535 40.214 Q 66.042 39.424 76.312 38.318 Q 86.582 37.212 95.509 35.869 Q 104.436 34.526 110.914 32.946 Z M 47.082 92.512 L 47.082 83.032 L 107.754 83.032 L 107.754 92.512 L 47.082 92.512 Z M 41.868 101.518 L 41.868 56.488 L 51.664 56.488 L 51.664 101.518 L 41.868 101.518 Z M 103.33 97.568 L 103.33 56.488 L 113.442 56.488 L 113.442 97.568 L 103.33 97.568 Z"/> </svg>
          <div tw="text-slate-900 ml-2 mt-1 font-semibold">
            Tsundoku
          </div>
        </div>
        <div v-if="dir" tw="flex items-center text-slate-600">
          <div v-if="dir" tw="text-4xl font-semibold mr-2" v-html="dir" />
          <svg v-if="dir === 'FAQ'" width="48" height="48" viewBox="0 -960 960 960" fill="currentColor" xmlns="http://www.w3.org/2000/svg"><path d="M120-55.5q-15.5 0-26.5-11T82.5-93q0-15.5 11-26.5t26.5-11h720q15.5 0 26.5 11t11 26.5q0 15.5-11 26.5t-26.5 11H120ZM207-170q-15.5 0-26.5-11t-11-26.5v-193q-32.5-54-50.75-114.25T100.5-639.5q0-59 15.25-117.5t36.75-116q7.5-20 24.5-32.25t38-12.25q26.5 0 44.75 18.5T276-854.5l-5.5 92q-3 48.5 11 93t41.75 78Q351-558 391-537.75t89 20.25q59.5 0 116.5 11.5T697-471.5q43.5 23 68.25 58.5T790-326v118.5q0 15.5-11 26.5t-26.5 11h-355v-32q0-35 24-60.25T480-287.5h120q15.5 0 26.5-11t11-26.5q0-15.5-11-26.5t-26.5-11H480q-66 0-111.75 47T322.5-202v32H207Zm273-392.5q-65.5 0-111.5-46t-46-111.5q0-65.5 46-111.5t111.5-46q65.5 0 111.5 46t46 111.5q0 65.5-46 111.5t-111.5 46Z" /></svg>
          <svg v-else-if="dir === 'Guide'" width="48" height="48" viewBox="0 -960 960 960" fill="currentColor" xmlns="http://www.w3.org/2000/svg"><path d="M831-327.5V-558L516-386.5q-17 9-36 9t-36-9l-329.5-180q-10-5.5-14.5-14.25t-4.5-18.75q0-10 4.5-18.75t14.5-14.25l330-179.5q8.5-5 17.25-7t18.25-2q9.5 0 18.25 2t17.25 7l371 202q9 5 14.25 13.75T906-577v249.5q0 15.5-11 26.5t-26.5 11q-15.5 0-26.5-11t-11-26.5Zm-387 173-197-107q-18.5-10-28.75-27.75T208-328v-149.5L444-349q17 9 36 9t36-9l236-128.5V-328q0 21-10.25 38.75T713-261.5l-197 107q-8.5 5-17.5 7t-18.5 2q-9.5 0-18.5-2t-17.5-7Z" /></svg>
          <svg v-else-if="dir === 'News'" width="48" height="48" viewBox="0 -960 960 960" fill="currentColor" xmlns="http://www.w3.org/2000/svg"><path d="M157.5-122.5q-31 0-53-22t-22-53v-614q0-6.5 5.5-9t10.5 2.5l35.5 35.5q5.5 5.5 13 5.25t13-5.75l40-40q5.5-5.5 13-5.75t13 5.25l41 41q5.5 5.5 13 5.5t13-5.5l41-41q5.5-5.5 13-5.25t13 5.75l40 40q5.5 5.5 13 5.75t13-5.25l41-41q5.5-5.5 13-5.5t13 5.5l41 41q5.5 5.5 13 5.25t13-5.75l40-40q5.5-5.5 13-5.75t13 5.25l41 41q5.5 5.5 13 5.5t13-5.5l41-41q5.5-5.5 13-5.25t13 5.75l40 40q5.5 5.5 13 5.75t13-5.25l35.5-35.5q5-5 10.5-2.5t5.5 9v614q0 31-22 53t-53 22h-645Zm0-75h285v-245h-285v245Zm360 0h285v-85h-285v85Zm0-160h285v-85h-285v85Zm-360-160h645v-125h-645v125Z" /></svg>
          <svg v-else-if="dir === 'Changelog'" width="48" height="48" viewBox="0 0 24 24" fill="currentColor" xmlns="http://www.w3.org/2000/svg"><path d="M20 3H4c-1.11 0-2 .89-2 2v14c0 1.11.89 2 2 2h16c1.11 0 2-.89 2-2V5c0-1.11-.89-2-2-2M5 7h5v6H5zm14 10H5v-2h14zm0-4h-7v-2h7zm0-4h-7V7h7z" /></svg>
        </div>
      </div>
      <div
        tw="w-full pr-0 flex flex-col items-start"
        :style="dir === 'Changelog' ? 'margin-top:auto;margin-bottom:auto;' : ''"
      >
        <div tw="text-6xl font-bold text-slate-900" v-html="title" />
        <div v-if="description" tw="mt-2 text-4xl text-slate-600" style="display: flex; flex-direction: column" v-html="description" />
      </div>
      <div v-if="dir === 'Changelog'" tw="w-full text-4xl text-slate-600" style="text-align: right">
        View full changelog for all changes
      </div>
    </div>
    <div tw="shrink-0 h-2 w-full flex" style="background-color: #0058a0;" />
  </div>
</template>
