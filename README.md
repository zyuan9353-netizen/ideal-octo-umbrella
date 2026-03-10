<!DOCTYPE html>
<html class="dark" lang="zh-CN">
<head>
    <meta charset="utf-8"/>
    <meta content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" name="viewport"/>
    <title>AI歌词配置预览工作台</title>
    <script src="https://cdn.tailwindcss.com?plugins=forms"></script>
    <link href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:wght@300;400;600" rel="stylesheet"/>
    <style>
        /* 基础样式回归自然滚动 */
        body { background-color: #1a1611; font-family: "PingFang SC", "Microsoft YaHei", sans-serif; margin: 0; padding: 0; }
        
        /* 预览模式下的歌词染色 */
        .ktv-lyric-box {
            font-size: 2rem;
            font-weight: 900;
            color: rgba(255,255,255,0.2);
            position: relative;
            display: inline-block;
            line-height: 1.4;
        }
        @media (max-width: 768px) { .ktv-lyric-box { font-size: 1.5rem; } }

        .ktv-lyric-fill {
            position: absolute;
            top: 0; left: 0;
            color: #f99406;
            overflow: hidden;
            white-space: nowrap;
            width: var(--fill-width, 0%);
            transition: width 0.1s linear;
        }

        /* 隐藏滚动条 */
        .no-scrollbar::-webkit-scrollbar { display: none; }
        
        /* 模拟 QQ 音乐进度条 */
        .qq-slider { height: 40px; display: flex; align-items: center; cursor: pointer; position: relative; width: 100%; }
        .qq-slider-track { height: 3px; background: rgba(255,255,255,0.1); width: 100%; border-radius: 2px; position: relative; }
        .qq-slider-fill { height: 100%; background: #f99406; width: 0%; border-radius: 2px; position: absolute; }
        .qq-slider-thumb { width: 12px; height: 12px; background: #fff; border-radius: 50%; position: absolute; top: 50%; transform: translate(-50%, -50%); box-shadow: 0 0 8px rgba(249,148,6,0.5); }

        .glass { background: rgba(35, 30, 24, 0.9); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.05); }
        .hidden { display: none !important; }

        /* 强制预览模式全屏锁定 */
        .preview-active { position: fixed; inset: 0; overflow: hidden; z-index: 100; background: #000; }
    </style>
</head>
<body class="text-slate-100 min-h-screen">

<!-- 顶部导航 -->
<header class="h-14 border-b border-white/5 flex items-center justify-between px-6 bg-[#1a1611] sticky top-0 z-[110]">
    <div class="flex items-center gap-3">
        <button id="btnBack" onclick="exitPreview()" class="hidden material-symbols-outlined text-[#f99406]">arrow_back</button>
        <span class="material-symbols-outlined text-[#f99406] text-2xl font-bold">album</span>
        <h1 class="text-[10px] font-black tracking-widest uppercase">AI Lyric <span class="text-[#f99406]">Station</span></h1>
    </div>
</header>

<!-- 【页面一：配置中心】 -->
<!-- 取消了固定高度，允许手机正常往下滑动 -->
<div id="pageConfig" class="p-4 lg:p-10 flex flex-col lg:flex-row gap-6 lg:gap-10">
    
    <!-- AI识别模块 (橙色) -->
    <section class="flex-1 glass rounded-[2.5rem] p-6 md:p-8 flex flex-col gap-6 border-t-2 border-[#f99406]">
        <h2 class="text-lg font-bold flex items-center gap-2"><span class="material-symbols-outlined text-[#f99406]">psychology</span>识别歌词文件预览</h2>
        
        <div class="space-y-4">
            <div class="space-y-1">
                <label class="text-[10px] text-slate-500 font-bold uppercase ml-1">视频ID</label>
                <input type="text" id="ai_vid" placeholder="输入视频ID锁定库资源" class="w-full bg-white/5 border-white/10 rounded-2xl py-3 px-4 text-sm focus:ring-[#f99406]">
            </div>
            
            <div class="grid grid-cols-2 gap-4">
                <input type="text" id="ai_song" placeholder="歌曲名称" class="bg-white/5 border-white/10 rounded-xl py-3 px-4 text-sm">
                <input type="text" placeholder="歌手名称" class="bg-white/5 border-white/10 rounded-xl py-3 px-4 text-sm">
            </div>
            
            <input type="text" placeholder="专辑名称 (综艺名称)" class="w-full bg-white/5 border-white/10 rounded-xl py-3 px-4 text-sm">
            
            <select class="w-full bg-white/5 border-white/10 rounded-xl py-3 px-4 text-sm">
                <option>中文 / 英文 / 粤语 / 混和</option>
                <option>纯中文</option>
                <option>纯英文</option>
            </select>

            <div class="flex gap-6 p-1">
                <label class="flex items-center gap-2 text-xs"><input type="radio" name="r1" checked class="text-[#f99406]"> 识别声音</label>
                <label class="flex items-center gap-2 text-xs"><input type="radio" name="r1" class="text-[#f99406]"> 不识别</label>
            </div>

            <button onclick="enterPreview('AI')" class="w-full bg-[#f99406] text-black font-black py-4 rounded-2xl shadow-xl shadow-orange-900/20 active:scale-95 transition-all">生成歌词并进入预览</button>
        </div>
    </section>

    <!-- 本地歌词绑定模块 (蓝色按钮) -->
    <section class="flex-1 glass rounded-[2.5rem] p-6 md:p-8 flex flex-col gap-6 border-t-2 border-blue-600">
  # ideal-octo-umbrella
