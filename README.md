<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>GCT公式化诋毁 · 无限浮光字</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none; /* 避免拖动文字影响体验，但无伤大雅 */
        }

        body {
            min-height: 100vh;
            background: radial-gradient(circle at 20% 30%, #0a0f1e, #03060c);
            overflow-x: hidden;
            font-family: "PingFang SC", "Microsoft YaHei", "Helvetica Neue", system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            position: relative;
        }

        /* 标题区域：一个大的闪闪发光的标题 */
        .hero {
            text-align: center;
            padding: 2rem 1rem;
            position: relative;
            z-index: 10;
            backdrop-filter: blur(3px);
        }

        .glow-title {
            font-size: 2.2rem;
            font-weight: 900;
            letter-spacing: 4px;
            background: linear-gradient(135deg, #fff8e7, #f5e56b, #ffd966, #f7c56e, #ffe6a3);
            background-size: 300% 300%;
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            animation: shineText 2.2s ease infinite, subtlePulse 1.8s ease-in-out infinite;
            text-shadow: 0 0 8px rgba(255,215,0,0.6), 0 0 18px rgba(255,200,0,0.4);
        }

        @keyframes shineText {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        @keyframes subtlePulse {
            0% { opacity: 0.9; text-shadow: 0 0 5px gold; }
            100% { opacity: 1; text-shadow: 0 0 22px #ffcc44; }
        }

        .sub {
            color: #e0dbb8;
            margin-top: 8px;
            font-size: 0.85rem;
            opacity: 0.8;
            letter-spacing: 1px;
        }

        /* 漂浮层容器 */
        .floating-container {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;  /* 让所有飘浮文字都不干扰点击，只用于展示 */
            z-index: 20;
            overflow: hidden;
        }

        /* 单个漂浮文字卡片 */
        .float-word {
            position: absolute;
            white-space: nowrap;
            font-weight: 800;
            font-size: 1.3rem;
            pointer-events: none;
            will-change: transform, opacity;
            filter: drop-shadow(0 0 6px currentColor);
            animation: floatUp linear forwards, glitter 1.2s steps(2) infinite;
            text-shadow: 0 0 12px rgba(255, 235, 140, 0.9);
            background: linear-gradient(120deg, #fff3c9, #ffea9e, #ffd966);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            font-weight: 900;
        }

        /* 上升动画，速度随机由js控制 */
        @keyframes floatUp {
            0% {
                transform: translateY(100vh) rotate(0deg);
                opacity: 0.9;
            }
            80% {
                opacity: 0.9;
            }
            100% {
                transform: translateY(-20vh) rotate(8deg);
                opacity: 0;
            }
        }

        /* 闪亮闪烁效果 (闪闪发光核心) */
        @keyframes glitter {
            0% { 
                text-shadow: 0 0 2px #f9e45b, 0 0 6px #ffc857, 0 0 12px #ffb347;
                filter: brightness(1);
            }
            50% { 
                text-shadow: 0 0 12px #ffef9e, 0 0 20px #ffdd77, 0 0 28px #ffbb33;
                filter: brightness(1.2);
            }
            100% { 
                text-shadow: 0 0 4px #f9e56c, 0 0 10px #ffcc5c, 0 0 14px #ffaa33;
                filter: brightness(0.95);
            }
        }

        /* 增加一些随机旋转的微调 */
        @keyframes slightRotate {
            from { transform: rotate(-2deg); }
            to { transform: rotate(3deg); }
        }

        /* 可选底部小提示 */
        .info-tip {
            position: fixed;
            bottom: 18px;
            left: 0;
            right: 0;
            text-align: center;
            font-size: 12px;
            color: #b9b27e;
            background: rgba(0,0,0,0.4);
            width: fit-content;
            margin: 0 auto;
            padding: 6px 18px;
            border-radius: 60px;
            backdrop-filter: blur(12px);
            z-index: 30;
            pointer-events: none;
            font-family: monospace;
        }

        /* 针对移动端优化字体大小 */
        @media (max-width: 600px) {
            .glow-title {
                font-size: 1.6rem;
            }
            .float-word {
                font-size: 1rem;
            }
        }
    </style>
</head>
<body>

<div class="hero">
    <div class="glow-title">✨徐烨可爱呢  ✨</div>
    <div class="sub">∞ 无限浮现 · 永恒闪耀 ∞</div>
</div>

<div class="floating-container" id="floatContainer"></div>
<div class="info-tip">⚡ 每颗字都在发光 | 无限新生 ⚡</div>

<script>
    (function() {
        // 核心文本
        const BASE_TEXT = "徐烨可爱呢"
       
        const variants = [
            "徐烨可爱呢",
            " 徐烨可爱呢 ",
            "⚡徐烨可爱呢⚡",
            "💥 徐烨可爱呢 💥",
            "徐烨可爱呢",
            "徐烨可爱呢"
        ];
        
        const container = document.getElementById('floatContainer');
        if (!container) return;
        
       
        const GENERATE_INTERVAL_MS = 350;   // 每秒约2~3个，无限浮现
        const MAX_ACTIVE_WORDS = 48;        // 同时最多存在50个，避免性能问题，但体验依然无限
        
        let activeWords = new Set(); // 存储当前活跃的元素，便于清理
        
        // 随机范围函数
        function randomRange(min, max) {
            return min + Math.random() * (max - min);
        }
        
        // 产生随机字号 (px 基准，但使用rem自适应)
        function getRandomFontSize() {
            // 手机端1rem=16px，但动态改变，一般范围 0.9rem ~ 2rem
            const min = 0.9;
            const max = 2.2;
            return randomRange(min, max) + 'rem';
        }
        
        // 随机颜色方案：金光闪闪 + 火焰色系 / 白金系 (背景clip已经做渐变，但为了更闪额外给颜色风格)
        // 其实由于背景渐变已经做了，额外加自定义颜色辅助更闪耀
        function getExtraGlowColor() {
            const colors = [
                '#FFE55C', '#FFD966', '#FFC857', '#FFB347', '#F9E076', '#FFE9A7'
            ];
            return colors[Math.floor(Math.random() * colors.length)];
        }
        
        // 随机左右位置 (百分比)
        function getRandomLeft() {
            // 让文字出现在整个屏幕宽度内，避免过于边缘也可以
            return randomRange(2, 98) + '%';
        }
        
        // 随机动画时长 (上升速度不同，营造错落漂浮感)
        function getRandomDuration() {
            return randomRange(3.5, 9.5) + 's';
        }
        
        // 随机延迟 (生成时就能错开轨迹)
        function getRandomDelay() {
            return randomRange(0, 1.2) + 's';
        }
        
        // 随机旋转起始偏移
        function getRandomRotate() {
            return randomRange(-6, 6) + 'deg';
        }
        
        // 生成单个漂浮词汇
        function createFloatingWord() {
            // 偶尔选取纯净版本，但大多数是显眼的GCT公式化诋毁
            let idx = Math.floor(Math.random() * variants.length);
            let text = variants[idx];
            // 为了确保核心信息不丢失，90%概率使用原始文本
            if (Math.random() < 0.7) {
                text = BASE_TEXT;
            }
            
            const wordSpan = document.createElement('div');
            wordSpan.className = 'float-word';
            wordSpan.innerText = text;
            
            // 设定样式
            wordSpan.style.left = getRandomLeft();
            wordSpan.style.fontSize = getRandomFontSize();
            // 自定义属性加强闪闪效果：额外发光色
            const glowColor = getExtraGlowColor();
            wordSpan.style.setProperty('--glow-color', glowColor);
            // 修改动画时长和延时
            const duration = getRandomDuration();
            const delay = getRandomDelay();
            wordSpan.style.animation = `floatUp ${duration} linear forwards, glitter 1.2s infinite`;
            wordSpan.style.animationDelay = delay;
            
            // 增加随机旋转起始transform偏移，使得不单调
            const startRotate = getRandomRotate();
            wordSpan.style.transform = `rotate(${startRotate})`;
            // 但动画floatUp里会覆盖transform的Y轴,但保留旋转变化，可微调
            // 为了让上升过程更灵动，再用js附加一个随机旋转关键帧,但简单通过额外内联样式也可
            // 增加一些整体模糊度？不需要，闪闪发光就够了
            
            // 设置轻微透明度随机范围 0.85~1
            wordSpan.style.opacity = randomRange(0.85, 1);
            
            // 清理函数，动画结束后移除元素
            const onFinish = () => {
                if (wordSpan && wordSpan.remove) {
                    wordSpan.remove();
                    activeWords.delete(wordSpan);
                }
                wordSpan.removeEventListener('animationend', onFinish);
            };
            
            wordSpan.addEventListener('animationend', onFinish);
            return wordSpan;
        }
        
        // 添加新词到容器，并维护数量不超过上限（但允许软限制：超过上限移除最早的一些）
        function addWordWithLimit() {
            // 控制活跃元素最大数量，若太多就先清理最早几个（性能友好）
            if (activeWords.size >= MAX_ACTIVE_WORDS) {
                const iterator = activeWords.values();
                const toRemove = [];
                // 最多移除 5 个最旧的
                let count = 0;
                for (let elem of iterator) {
                    if (count >= 6) break;
                    toRemove.push(elem);
                    count++;
                }
                for (let oldElem of toRemove) {
                    if (oldElem && oldElem.remove) {
                        oldElem.remove();
                        activeWords.delete(oldElem);
                    }
                }
            }
            
            const newWord = createFloatingWord();
            if (newWord) {
                container.appendChild(newWord);
                activeWords.add(newWord);
            }
        }
        
        // 启动无限生成器，每隔一定时间添加一个飘浮闪闪发光的文字
        let intervalId = setInterval(() => {
            addWordWithLimit();
        }, GENERATE_INTERVAL_MS);
        
        // 另外还要额外做一个批量补正：为了视觉上更丰富，刚进入页面时瞬间生成8个
        for (let i = 0; i < 12; i++) {
            setTimeout(() => {
                addWordWithLimit();
            }, i * 70);
        }
        
        // 当页面可见性变化时也可以保持生成，无影响，用户离开后再回来仍然无限
        // 如果不需要暂停，无需额外处理
        
        // 可选：增加页面滚动、触摸不会有任何副作用
        // 也保证在微信里浏览器也流畅运行，监听resize其实无所谓
        
        // 为了避免内存泄漏，页面关闭时清除定时器（但不是必须，单页应用无需担心）
        window.addEventListener('beforeunload', () => {
            if (intervalId) clearInterval(intervalId);
        });
        
        // 动态调节一下在移动端的性能，但生成速度已做稳妥限制
        // 如果觉得文字太多还可通过滑动监听 (不必要)
        // 另外为了更好的闪闪效果，增加一点动态给body添加呼吸背景
        // 高级一点: 偶尔改变悬浮文字背景色无必要，因为css已经足够闪。
        
        // 额外锦上添花：用requestAnimationFrame给任意新加的字增加额外辉光，但css已处理。
        console.log('✨ 无限浮光字已启动 | GCT公式化诋毁闪耀永恒 ✨');
        
        // 并且为了防止在微信内复制文字不方便——保留优雅，用户长按也不会有菜单干扰吗？ 但也不影响炫酷。
        // 完美符合：闪闪发光 + 无限浮现 + 字体内容指定。
        
    })();
</script>
</body>
</html>  
