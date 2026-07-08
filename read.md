代码制作思路 + 用到的核心属性 / 技术完整说明
一、整体制作流程（这段天气预报页面是如何做出来的）
1. 需求拆解（对照题目 5 项功能）
先拆分任务要求，分 5 大模块开发：
城市搜索模块：输入框、搜索按钮、回车触发、非空校验
实时天气展示模块：城市名、当前温度、天气状况
未来 3 天预报模块：循环渲染每日日期、天气、高低温
自动定位加分模块：调用浏览器定位 API，获取经纬度查本地天气
本地持久化加分模块：localStorage 缓存上次搜索城市，页面刷新自动加载
2. 分层结构开发（从上到下分层写代码）
HTML 结构搭建
搭建页面骨架：标题、定位按钮、搜索栏、错误提示区、天气卡片（实时天气 + 3 天预报），划分清晰的容器层级，用 id 绑定 JS 操作元素。
CSS 样式美化
使用现代渐变背景、毛玻璃卡片、弹性布局适配手机 / 电脑，增加淡入动画、hover 交互、输入框聚焦效果，统一配色变量方便维护。
JS 交互逻辑开发（核心）
两套免费开源无密钥 API：地理编码 API（城市名转经纬度）、气象 API（根据经纬度获取真实天气）
封装通用函数：错误提示、天气代码转中文描述、请求天气、城市转坐标
绑定交互事件：点击搜索、回车搜索、定位按钮点击
页面加载生命周期：读取本地缓存自动加载历史城市
3. 容错与优化
增加网络捕获、城市不存在提示、定位失败提示、空输入拦截；动画优化页面切换体验；弹性布局自适应手机屏幕。

二、HTML 用到核心标签与属性
基础页面属性
<meta charset="UTF-8">：设置页面中文编码，防止中文乱码
<meta name="viewport" content="width=device-width, initial-scale=1.0">：移动端自适应，适配手机屏幕
<title>：网页标题
布局容器
<div class="container">：根容器，承载所有内容，统一居中、圆角卡片样式
交互组件
<input type="text" id="city-input" placeholder="输入城市名">：文本输入框
placeholder：输入框默认提示文字
id：唯一标识，供 JS 获取 DOM 元素
<button id="search-btn"> 按钮：点击触发搜索逻辑
<button id="location-btn">：定位功能按钮
文本 / 展示标签
<h1>：大标题；<p>：错误提示文字
多层嵌套 div 区分「实时天气」「预报列表」模块，实现模块化布局
  
三、CSS 运用的核心属性 & 技术
1. 弹性布局 Flex（核心布局）
display: flex：弹性盒子，实现水平排列
justify-content: center：水平居中
align-items: center：垂直居中
gap: 10px：子元素之间统一间距，替代 margin
flex: 1：输入框自动占满剩余宽度，按钮固定宽度
2. 背景相关属性
background: var(--bg-gradient)：渐变背景
backdrop-filter: blur(10px)：毛玻璃磨砂效果（卡片半透明模糊）
rgba(255,255,255,0.95)：透明白色背景，实现半透卡片
3. 圆角、阴影美化
border-radius: 20px：圆角，柔和卡片视觉
box-shadow: 0 10px 25px rgba(0,0,0,0.1)：卡片阴影，分层立体感
4. 过渡与交互动画
transition: 0.3s：hover、聚焦时平滑过渡效果
input:focus { border-color: var(--primary-color); }：输入框聚焦高亮边框
button:hover：鼠标悬浮按钮变色，提升交互反馈
@keyframes fadeIn + animation: fadeIn 0.5s ease：天气卡片淡入动画，加载时平滑出现
5. 尺寸与响应式
max-width: 450px：容器最大宽度，电脑端不会过宽
width: 100%：移动端自动铺满屏幕
font-size: rem：相对单位，适配不同设备字体大小
6. 显示控制
display: none：默认隐藏天气卡片、错误提示，查询成功再切换为可见
  
四、JavaScript 运用核心技术、API、语法
1. DOM 操作（页面元素控制）
document.getElementById("id名")：获取页面输入框、按钮、展示区域元素
element.textContent = "文字"：动态修改页面文字（城市、温度、天气）
element.style.display = "block/none"：控制模块显示 / 隐藏
forecastList.innerHTML += itemHtml：动态拼接 HTML，渲染 3 天预报列表
2. 异步网络请求（Fetch API）
fetch()：浏览器原生接口，发起网络请求调用免费天气开放 API
搭配 async / await 异步语法，同步写法处理异步网络请求；
try...catch 捕获网络异常、接口报错，防止页面崩溃。
用到两套开源免费接口：
地理编码接口：城市名 → 经纬度坐标
气象预报接口：经纬度 → 实时温度、天气代码、3 天高低温预报
3. 浏览器原生 API（加分功能核心）
Geolocation API（自动定位）
navigator.geolocation.getCurrentPosition()：调用浏览器获取用户当前经纬度，实现一键定位本地天气。
localStorage 本地持久化存储（加分项）
localStorage.setItem('lastCity', cityName)：保存上次搜索城市到浏览器本地
localStorage.getItem('lastCity')：页面刷新时读取缓存城市，自动查询历史天气
4. 事件监听（交互逻辑）
click 点击事件：按钮点击触发搜索 / 定位
keypress 键盘事件：监听回车键，回车一键搜索
window.addEventListener('load')：页面加载完成后执行，读取本地缓存
5. 数据处理工具
对象映射表 getWeatherDesc(code)：数字天气代码转中文天气文字（0 = 晴朗、95 = 雷雨）
字符串截取 daily.time[i].slice(5)：日期字符串截取，只保留月 - 日
Math.round()：温度四舍五入取整数，展示更美观
数组循环 for(let i = 1; i <= 3; i++)：循环生成 3 天预报卡片
6. 输入校验 & 异常处理
判断输入框value.trim()为空，直接弹出错误提示，不发起无效网络请求
判断接口返回空数据、定位权限拒绝、网络失败，统一调用showError()函数展示红色错误文字，用户友好提示
