<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>徐烨 · 爱心祝福漫游</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none; /* 避免拖动干扰，不影响视觉 */
            -webkit-tap-highlight-color: transparent;
        }

        body {
            min-height: 100vh;
            background: radial-gradient(circle at 30% 20%, #1a0b1f, #0a0510);
            display: flex;
            align-items: center;
            justify-content: center;
            overflow: hidden;
            font-family: "PingFang SC", "Microsoft YaHei", "Helvetica Neue", system-ui, -apple-system, 'Segoe UI', Roboto, 'Noto Sans CJK SC', sans-serif;
            position: relative;
            touch-action: pan-x pan-y; /* 保留滑动，但爱心区域不干扰点击 */
        }

        /* 画布容器：全屏，爱心粒子绝对定位 */
        .heart-canvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;  /* 让所有文字不干扰点击，只用于视觉 */
            z-index: 10;
        }

        /* 单个祝福词语样式 —— 闪闪发光的彩色字 */
        .bless-word {
            position: absolute;
            white-space: nowrap;
            font-weight: 700;
            font-size: 1rem;
            pointer-events: none;
            will-change: transform, opacity, filter;
            filter: drop-shadow(0 0 4px currentColor);
            text-shadow: 0 0 6px rgba(255,255,180,0.7);
            animation: shimmerFloat 3s infinite ease-in-out, gentleColorShift 2s infinite alternate;
            transition: transform 0.2s;
            z-index: 20;
            font-family: inherit;
            letter-spacing: 1px;
        }

        /* 发光浮动的通用动画 — 让爱心整体有呼吸感，而每个字轻微浮动 */
        @keyframes shimmerFloat {
            0% {
                transform: translateY(0px) scale(1);
                filter: drop-shadow(0 0 2px currentColor);
            }
            100% {
                transform: translateY(-3px) scale(1.02);
                filter: drop-shadow(0 0 12px currentColor);
            }
        }

        /* 颜色流动辅助 — 但颜色主要靠js动态改变，css作为后备渐变更加炫彩 */
        @keyframes gentleColorShift {
            0% {
                text-shadow: 0 0 4px #ffb347, 0 0 8px #ffaa44;
            }
            100% {
                text-shadow: 0 0 12px #ff66cc, 0 0 14px #ff44aa;
            }
        }

        /* 标题区域：浪漫氛围 */
        .title-card {
            position: absolute;
            bottom: 8%;
            left: 0;
            right: 0;
            text-align: center;
            z-index: 30;
            pointer-events: none;
            background: rgba(0,0,0,0.4);
            backdrop-filter: blur(12px);
            width: 85%;
            margin: 0 auto;
            border-radius: 80px;
            padding: 12px 20px;
            left: 50%;
            transform: translateX(-50%);
            border: 1px solid rgba(255,215,150,0.5);
            box-shadow: 0 0 20px rgba(255,100,150,0.3);
        }

        .title-name {
            font-size: 1.6rem;
            font-weight: 800;
            background: linear-gradient(135deg, #FFD966, #FF85B3, #A5F0FF, #FFB347);
            background-size: 300% 300%;
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            animation: gradientWave 3s ease infinite;
            letter-spacing: 2px;
        }

        @keyframes gradientWave {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        .sub-love {
            font-size: 0.75rem;
            color: #ffe6c7;
            margin-top: 5px;
            opacity: 0.9;
            letter-spacing: 1px;
        }

        /* 底部小提示 */
        .info-tip {
            position: absolute;
            top: 12px;
            right: 16px;
            background: rgba(20,10,25,0.65);
            backdrop-filter: blur(4px);
            border-radius: 50px;
            padding: 6px 12px;
            font-size: 10px;
            color: #ffdebd;
            z-index: 35;
            font-family: monospace;
            pointer-events: none;
        }

        /* 适配小屏幕文字大小自适应 */
        @media (max-width: 550px) {
            .bless-word {
                font-size: 0.85rem;
            }
            .title-name {
                font-size: 1.3rem;
            }
        }

        @media (max-width: 380px) {
            .bless-word {
                font-size: 0.75rem;
            }
        }

        /* 爱心整体区域呼吸光晕 */
        .heart-glow {
            position: fixed;
            top: 50%;
            left: 50%;
            width: 80vw;
            height: 80vw;
            transform: translate(-50%, -50%);
            background: radial-gradient(circle, rgba(255,80,120,0.15) 0%, rgba(255,40,80,0) 70%);
            border-radius: 50%;
            pointer-events: none;
            z-index: 5;
            animation: pulseGlow 3s infinite alternate;
        }

        @keyframes pulseGlow {
            0% { opacity: 0.3; transform: translate(-50%, -50%) scale(0.9);}
            100% { opacity: 0.7; transform: translate(-50%, -50%) scale(1.1);}
        }
    </style>
</head>
<body>

<div class="heart-glow"></div>
<div class="heart-canvas" id="heartCanvas"></div>

<div class="title-card">
    <div class="title-name">💖 徐烨 · 心之所向 💖</div>
    <div class="sub-love">✨ 万千祝福 · 爱意成心 ✨</div>
</div>
<div class="info-tip">❤️ 五光十色 · 爱心祝福阵 ❤️</div>

<script>
    (function() {
        // ----------------------------- 祝福词库 (随机拼接到 "徐烨" 后面) -----------------------------
        const GOOD_WISHES = [
            "身体健康", "万事如意", "心想事成", "前程似锦", "笑口常开",
            "幸福美满", "平安喜乐", "财运亨通", "好运连连", "青春永驻",
            "事业腾飞", "甜蜜相伴", "星光璀璨", "梦想成真", "四季如春",
            "福气满满", "快乐无边", "温柔常驻", "光芒万丈", "未来可期",
            "朝气蓬勃", "顺遂无虞", "喜乐安宁", "明朗可爱", "花开富贵"
        ];
        
        // 名字固定前缀 (每个祝福语都是 "徐烨" + 随机后缀)
        const NAME_PREFIX = "徐烨";
        
        // 获取随机祝福短语
        function getRandomBlessing() {
            const randomIndex = Math.floor(Math.random() * GOOD_WISHES.length);
            return GOOD_WISHES[randomIndex];
        }
        
        // 生成完整祝福语: "徐烨" + 后缀 (避免重复，但随机即可，爱心需多样性)
        function createFullBlessing() {
            const suffix = getRandomBlessing();
            // 偶尔加一颗小爱心增加氛围但保留文字清晰
            const heartEmojis = [" ❤️", " 💖", " ✨", ""];
            const randomEmoji = heartEmojis[Math.floor(Math.random() * heartEmojis.length)];
            return `${NAME_PREFIX} · ${suffix}${randomEmoji}`;
        }
        
        // ---------- 爱心参数方程: 生成心形点集 (密度较高, 适配手机屏幕) ----------
        // 心形公式: x = 16 * sin(t)^3 , y = 13cos(t) - 5 cos(2t) - 2cos(3t) - cos(4t)
        // 经典缩放系数，为了让爱心占满手机大部分区域但不出界，映射坐标范围 -1..1 区域再映射到屏幕宽高
        // 偏移中心，屏幕比例自适应
        
        let heartPoints = [];      // 存储 {xRatio, yRatio} 范围 -1..1 标准化坐标
        
        function generateHeartPoints(numPoints = 240) {
            const points = [];
            // 心形极坐标风格：使用参数 t 0~2PI
            for (let i = 0; i < numPoints; i++) {
                const t = (i / numPoints) * Math.PI * 2;
                // 经典心形公式
                let xRaw = 16 * Math.pow(Math.sin(t), 3);
                let yRaw = 13 * Math.cos(t) - 5 * Math.cos(2*t) - 2*Math.cos(3*t) - Math.cos(4*t);
                // 归一化范围：x大约在 -16..16, y大约在 -16..15 左右，进行归一化映射 -1..1
                // 根据实际极值调整，让心尖朝下居中
                const maxRange = 18;
                let xNorm = xRaw / maxRange;
                let yNorm = yRaw / maxRange;
                // 心形默认尖角向下，但通常心形上下需要微调让视觉更好看，y轴向下为正，调整偏移让爱心视觉中心在屏幕中央偏上一点
                // 不做额外反转，直接使用，后续映射时，yNorm 负数向上，正数向下；但为了让爱心正立，不做翻转。
                points.push({ x: xNorm, y: yNorm });
            }
            // 再额外增加底部尖角区域更饱满，再细微增加内围点？ 但240个已经够密集
            // 但为了让爱心更圆满，插入额外随机偏移也可，使用爱心变形稳定即可。
            return points;
        }
        
        // 重新构建所有爱心祝福语 (根据当前屏幕尺寸重新计算位置，并生成新的DOM元素)
        let currentWords = [];     // 存储当前活动元素的引用 {dom, pointIndex}
        let animationFrameId = null;
        let resizeTimeout = null;
        let container = document.getElementById('heartCanvas');
        
        // 颜色轮转器: 让每一个字单独拥有独立HSL颜色且随时间呈动态变化，做到五颜六色变动
        // 我们为每个单词分配一个色相偏移基值，然后动画循环中更新每个字的颜色 (hue 随时间飘移)
        // 赋予每个祝福语独特且活泼的色彩转变
        
        class ColorfulWord {
            constructor(domElement, baseHueOffset) {
                this.dom = domElement;
                this.baseHue = baseHueOffset;      // 0~360 基础偏移
                this.timeOffset = Math.random() * Math.PI * 2;
                this.lastTimestamp = 0;
            }
            
            // 更新颜色, 参数 globalTime 毫秒，让色调流动
            updateColor(now) {
                if (!this.dom) return;
                // 色相在主色调附近波动 随时间流动，每个字不同速度
                // 速度系数 0.002 ~ 0.008 让彩色流动舒服
                const speed = 0.0035;
                let hue = (this.baseHue + now * speed) % 360;
                // 饱和度和亮度保持鲜艳: 饱和度75%～95%，亮度65%～85% 让字耀眼
                const sat = 75 + Math.sin(now * 0.002 + this.timeOffset) * 15;   // 60~90%
                const light = 65 + Math.cos(now * 0.0018 + this.baseHue * 0.01) * 15; // 55~80%
                const color = `hsl(${hue}, ${sat}%, ${light}%)`;
                // 添加文字阴影和发光效果同色系但更亮
                this.dom.style.color = color;
                // 动态改变文字阴影，让闪闪发光效果更加明显
                const glowIntensity = 0.5 + Math.sin(now * 0.008 + this.baseHue) * 0.3;
                this.dom.style.textShadow = `0 0 ${4 + glowIntensity * 8}px ${color}, 0 0 8px rgba(255,200,100,0.7)`;
                // 轻微缩放呼吸由CSS负责，颜色由js彻底控制
            }
        }
        
        let wordColorControllers = [];  // 存储每个祝福语的颜色控制实例
        let lastTimestampColor = 0;
        
        // 清除所有现有的爱心祝福词
        function clearAllWords() {
            if (container) {
                while (container.firstChild) {
                    container.removeChild(container.firstChild);
                }
            }
            currentWords = [];
            wordColorControllers = [];
        }
        
        // 根据当前视口尺寸与标准化爱心点集，创建或重新创建所有祝福语
        function buildHeartBlessings() {
            if (!container) return;
            clearAllWords();
            
            const points = generateHeartPoints(260);  // 大约260个祝福词，爱心浓密
            const screenWidth = window.innerWidth;
            const screenHeight = window.innerHeight;
            // 定义爱心占位比例: 希望爱心整体宽70%，高65%，居中且位置偏上一些（经典爱心效果）
            const scaleX = screenWidth * 0.68;
            const scaleY = screenHeight * 0.62;
            const offsetX = screenWidth / 2;
            const offsetY = screenHeight / 2 - screenHeight * 0.02; // 微微上移更优雅
            
            // 为了使爱心更圆润，避免边缘过于靠边，对超出边缘做限制但不用太严格
            for (let i = 0; i < points.length; i++) {
                const p = points[i];
                // 映射坐标: 心形标准坐标范围-1..1 左右，但经典心形宽高比自然，缩放比例适当
                let xPos = offsetX + p.x * scaleX;
                let yPos = offsetY + p.y * scaleY * 0.92; // y轴微调，让爱心形状更自然
                // 避免超出屏幕边缘太厉害，进行软裁剪
                xPos = Math.min(Math.max(xPos, 20), screenWidth - 20);
                yPos = Math.min(Math.max(yPos, 40), screenHeight - 70);
                
                // 生成祝福语文字
                const blessingText = createFullBlessing();
                const wordDiv = document.createElement('div');
                wordDiv.className = 'bless-word';
                wordDiv.innerText = blessingText;
                wordDiv.style.left = xPos + 'px';
                wordDiv.style.top = yPos + 'px';
                // 随机旋转微小角度让爱心更俏皮
                const rotateVal = (Math.random() - 0.5) * 8;
                wordDiv.style.transform = `rotate(${rotateVal}deg)`;
                // 字体大小根据屏幕宽度略有区分，爱心边缘也可以用稍小字号，中心不变；统一随机稍微调整增加层次
                let fontSize = 0.85 + Math.random() * 0.5; // rem单位
                if (screenWidth < 400) fontSize = 0.75 + Math.random() * 0.45;
                wordDiv.style.fontSize = `${fontSize}rem`;
                wordDiv.style.fontWeight = '700';
                // 初始白色透明，之后动画会马上给颜色
                wordDiv.style.color = '#fff0c0';
                wordDiv.style.opacity = '0.95';
                // 增加自定义动画持续时间微调
                const duration = 2.5 + Math.random() * 2;
                wordDiv.style.animation = `shimmerFloat ${duration}s infinite ease-in-out`;
                wordDiv.style.animationDelay = `${Math.random() * 1.5}s`;
                
                container.appendChild(wordDiv);
                
                // 存储DOM以及对应点信息(后续颜色控制)
                currentWords.push({
                    dom: wordDiv,
                    pointX: xPos,
                    pointY: yPos,
                    baseRotate: rotateVal
                });
                // 为每个字分配独立色相偏移 (0~360)
                const hueOffset = Math.random() * 360;
                const colorCtrl = new ColorfulWord(wordDiv, hueOffset);
                wordColorControllers.push(colorCtrl);
            }
            
            // 可选：额外增加一些心形内部飘浮粒子，为了更饱满，再在心形中间随机加20个祝福语？但已经足够紧密，爱心成型
            // 为了让爱心更充实，在心脏内部区域再随机生成一些点 (基于心形内部插值)
            addInnerHeartPoints(screenWidth, screenHeight, offsetX, offsetY, scaleX, scaleY);
        }
        
        // 增加爱心内部填充（让爱心更饱满）
        function addInnerHeartPoints(screenWidth, screenHeight, centerX, centerY, scaleX, scaleY) {
            // 内部额外增加大约30~50个祝福词，基于心形内部区域随机采样
            const extraCount = Math.floor(screenWidth / 18);  // 根据屏幕大小 32~48个左右
            const pointsInternal = [];
            // 获取已有所有心形边缘点集，为了内部填充，采用简单蒙特卡洛法在心形包围盒内
            // 但为避免性能复杂，简单二次心形内部公式约束： (x^2 + (9/4)y^2 + ... ) 但用经典方法
            for (let i = 0; i < extraCount; i++) {
                // 随机在 -0.8~0.8 归一化范围内部尝试采点并满足心形内部距离
                let randX, randY;
                let valid = false;
                let attempts = 0;
                while (!valid && attempts < 25) {
                    randX = (Math.random() - 0.5) * 1.5;
                    randY = (Math.random() - 0.5) * 1.4;
                    // 心形隐函数近似: (x^2 + (9/4)y^2 + ... )? 使用参数心形边界简化：距离小于边界
                    // 用公式评估点在心形内部近似: 心形曲线公式值
                    const tVal = Math.atan2(randY, randX);
                    const heartRad = 16 * Math.pow(Math.sin(tVal), 3);
                    const heartRadY = 13 * Math.cos(tVal) - 5 * Math.cos(2*tVal) - 2*Math.cos(3*tVal) - Math.cos(4*tVal);
                    const normX = randX * 18;
                    const normY = randY * 18;
                    // 粗略内部判断：使用标准心形边界半径
                    const boundaryRadius = Math.abs(heartRad) * 0.9;
                    if (Math.abs(normX) < boundaryRadius + 2 && Math.abs(normY) < Math.abs(heartRadY) + 3) {
                        valid = true;
                    }
                    attempts++;
                }
                if (!valid) {
                    randX = (Math.random() - 0.5) * 1.0;
                    randY = (Math.random() - 0.5) * 0.9;
                }
                let xPos = centerX + randX * scaleX * 0.75;
                let yPos = centerY + randY * scaleY * 0.7;
                xPos = Math.min(Math.max(xPos, 15), screenWidth - 15);
                yPos = Math.min(Math.max(yPos, 40), screenHeight - 60);
                
                const blessingText = createFullBlessing();
                const wordDiv = document.createElement('div');
                wordDiv.className = 'bless-word';
                wordDiv.innerText = blessingText;
                wordDiv.style.left = xPos + 'px';
                wordDiv.style.top = yPos + 'px';
                const rotateVal = (Math.random() - 0.5) * 10;
                wordDiv.style.transform = `rotate(${rotateVal}deg)`;
                let fontSize = 0.8 + Math.random() * 0.55;
                if (screenWidth < 400) fontSize = 0.7 + Math.random() * 0.4;
                wordDiv.style.fontSize = `${fontSize}rem`;
                wordDiv.style.fontWeight = '600';
                wordDiv.style.opacity = '0.9';
                wordDiv.style.animation = `shimmerFloat ${2.2 + Math.random() * 2}s infinite ease-in-out`;
                container.appendChild(wordDiv);
                currentWords.push({ dom: wordDiv });
                const hueOff = Math.random() * 360;
                wordColorControllers.push(new ColorfulWord(wordDiv, hueOff));
            }
        }
        
        // 重新布局爱心，当屏幕旋转或窗口大小改变时 (适配手机横竖屏)
        function rebuildHeartResponsive() {
            if (resizeTimeout) clearTimeout(resizeTimeout);
            resizeTimeout = setTimeout(() => {
                if (container) {
                    buildHeartBlessings();
                    startColorAnimation();  // 重启颜色动画
                }
            }, 150);
        }
        
        // 颜色动画循环，让所有祝福语五颜六色持续变动
        let colorAnimActive = true;
        let lastFrameTime = 0;
        
        function startColorAnimation() {
            if (animationFrameId) {
                cancelAnimationFrame(animationFrameId);
                animationFrameId = null;
            }
            const animateColors = (timestamp) => {
                if (!colorAnimActive) return;
                // 使用timestamp让颜色动态流转
                if (wordColorControllers.length > 0) {
                    for (let ctrl of wordColorControllers) {
                        if (ctrl && ctrl.dom && ctrl.dom.parentNode) {
                            ctrl.updateColor(timestamp);
                        }
                    }
                }
                animationFrameId = requestAnimationFrame(animateColors);
            };
            animationFrameId = requestAnimationFrame(animateColors);
        }
        
        // 简单防抖监听窗口变化
        window.addEventListener('resize', () => {
            rebuildHeartResponsive();
        });
        
        // 初次渲染
        function init() {
            buildHeartBlessings();
            startColorAnimation();
            // 增加一个微小的心跳效果：定期随机轻微调整某些祝福语的透明度/辉光额外效果，但颜色已经可以
            // 再加每过8秒更换一部分祝福词的内容（随机刷新词库内容，让爱心的祝福词不单调但保留爱心结构）
            setInterval(() => {
                // 随机刷新部分祝福语的文本内容，最多更新30个，但保持爱心不变位置, 实现祝福语流动感
                if (currentWords.length === 0) return;
                const refreshCount = Math.min(25, Math.floor(currentWords.length * 0.2));
                for (let i = 0; i < refreshCount; i++) {
                    const randIndex = Math.floor(Math.random() * currentWords.length);
                    const wordObj = currentWords[randIndex];
                    if (wordObj && wordObj.dom) {
                        const newBless = createFullBlessing();
                        wordObj.dom.innerText = newBless;
                        // 为了让刷新有短暂光效，增加类瞬间闪白
                        wordObj.dom.style.transition = 'filter 0.2s';
                        wordObj.dom.style.filter = 'brightness(1.4)';
                        setTimeout(() => {
                            if(wordObj.dom) wordObj.dom.style.filter = '';
                        }, 200);
                    }
                }
                // 同时刷新色相控制器里的颜色，确保颜色依然五彩斑斓，不干预
            }, 5200);
        }
        
        // 确保在微信等浏览器中字体渲染平滑，监听页面显示
        window.addEventListener('load', () => {
            init();
        });
        
        // 页面可见性变化保持动画
        document.addEventListener('visibilitychange', () => {
            if (document.hidden) {
                colorAnimActive = false;
                if (animationFrameId) {
                    cancelAnimationFrame(animationFrameId);
                    animationFrameId = null;
                }
            } else {
                colorAnimActive = true;
                startColorAnimation();
            }
        });
        
        // 启动后备
        if (document.readyState === 'loading') {
            document.addEventListener('DOMContentLoaded', init);
        } else {
            init();
        }
        
        // 爱心呼吸感 & 触摸反馈都没有破坏体验
    })();
</script>
</body>
</html>
