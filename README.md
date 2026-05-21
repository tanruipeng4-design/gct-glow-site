<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>徐烨 · 炫彩爱心祝福</title>
<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
        -webkit-tap-highlight-color: transparent;
    }

    body {
        min-height: 100vh;
        background: linear-gradient(145deg, #0e0a1a 0%, #1a1025 100%);
        display: flex;
        align-items: center;
        justify-content: center;
        font-family: "PingFang SC", "Microsoft YaHei", "Helvetica Neue", system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
        touch-action: pan-x pan-y;
        overflow: hidden;
        position: fixed;
        width: 100%;
        height: 100%;
    }

    /* 主容器 */
    .love-container {
        position: relative;
        width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        overflow: hidden;
    }

    /* Canvas 画布 — 爱心文字展示区 */
    #heartCanvas {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        display: block;
        /* 优化字体渲染 */
        image-rendering: crisp-edges;
        image-rendering: -webkit-optimize-contrast;
    }

    /* 底部浪漫卡片 */
    .title-card {
        position: absolute;
        bottom: 6%;
        left: 50%;
        transform: translateX(-50%);
        background: rgba(0, 0, 0, 0.55);
        backdrop-filter: blur(20px);
        border-radius: 60px;
        padding: 10px 24px;
        text-align: center;
        z-index: 20;
        pointer-events: none;
        white-space: nowrap;
        border: 1px solid rgba(255, 200, 150, 0.5);
        box-shadow: 0 0 28px rgba(255, 80, 120, 0.3);
        width: auto;
        max-width: 85vw;
    }

    .title-name {
        font-size: 1.5rem;
        font-weight: 800;
        background: linear-gradient(135deg, #FFD966, #FF85B3, #6BCBFF, #FFB347);
        background-size: 300% 300%;
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
        animation: gradientFlow 2.8s ease infinite;
        letter-spacing: 2px;
    }

    @keyframes gradientFlow {
        0% { background-position: 0% 50%; }
        50% { background-position: 100% 50%; }
        100% { background-position: 0% 50%; }
    }

    .sub-love {
        font-size: 0.7rem;
        color: #ffddbb;
        margin-top: 6px;
        opacity: 0.9;
        letter-spacing: 1px;
    }

    /* 顶部装饰提示 */
    .info-badge {
        position: absolute;
        top: 16px;
        right: 16px;
        background: rgba(0, 0, 0, 0.5);
        backdrop-filter: blur(8px);
        padding: 4px 12px;
        border-radius: 40px;
        font-size: 10px;
        color: #ffcf9a;
        z-index: 25;
        font-family: monospace;
        pointer-events: none;
        letter-spacing: 1px;
    }

    /* 移动端适配，确保字体清晰 */
    @media (max-width: 560px) {
        .title-name {
            font-size: 1.2rem;
        }
        .sub-love {
            font-size: 0.6rem;
        }
        .info-badge {
            font-size: 8px;
            top: 12px;
            right: 12px;
        }
    }

    /* 防止页面滚动 */
    body {
        overflow: hidden;
        position: fixed;
    }
</style>
</head>
<body>
<div class="love-container">
    <canvas id="heartCanvas"></canvas>
    <div class="title-card">
        <div class="title-name">💖 徐烨 · 爱心祝福阵 💖</div>
        <div class="sub-love">✨ 五光十色 · 永驻心间 ✨</div>
    </div>
    <div class="info-badge">❤️ 每一字都是祝福 ❤️</div>
</div>

<script>
    (function(){
        // ************************ 祝福词库 (吉祥话) ************************
        const WISH_LIST = [
            "身体健康", "万事如意", "心想事成", "前程似锦", "笑口常开",
            "幸福美满", "平安喜乐", "财运亨通", "好运连连", "青春永驻",
            "事业腾飞", "甜蜜相伴", "星光璀璨", "梦想成真", "四季如春",
            "福气满满", "快乐无边", "温柔常驻", "光芒万丈", "未来可期",
            "朝气蓬勃", "顺遂无虞", "喜乐安宁", "明朗可爱", "花开富贵",
            "前程万里", "福星高照", "吉星高照", "如鱼得水", "龙马精神"
        ];
        
        const NAME = "徐烨";
        
        // 生成随机祝福语: "徐烨" + 吉祥话 (偶尔加小爱心点缀但不会影响清晰度)
        function getRandomBlessing() {
            const randomIndex = Math.floor(Math.random() * WISH_LIST.length);
            const wish = WISH_LIST[randomIndex];
            // 随机添加小符号增加活泼感，但不影响文字主体
            const decor = Math.random() > 0.7 ? " ✨" : (Math.random() > 0.8 ? " ❤️" : "");
            return `${NAME} · ${wish}${decor}`;
        }
        
        // 获取canvas元素
        const canvas = document.getElementById('heartCanvas');
        let ctx = null;
        let width = 0, height = 0;
        
        // 存储爱心上的文字对象数组: { text, x, y, fontSize, hueOffset, speed, angleOffset }
        let textItems = [];
        let animationId = null;
        let resizeTimer = null;
        
        // ---------- 经典心形坐标生成 (正爱心，尖角向下) ----------
        // 心形参数方程: x = 16 * sin(t)^3 , y = 13cos(t) - 5 cos(2t) - 2cos(3t) - cos(4t)
        // 返回标准化坐标范围: x ∈ [-1, 1], y ∈ [-1.1, 1.1] 左右，通过缩放平移适应屏幕
        function getHeartPoint(t) {
            const xRaw = 16 * Math.pow(Math.sin(t), 3);
            const yRaw = 13 * Math.cos(t) - 5 * Math.cos(2*t) - 2 * Math.cos(3*t) - Math.cos(4*t);
            // 归一化范围: x: -16..16  -> -1..1 ; y: -16..15  -> -1..1
            const maxRange = 17;
            let xNorm = xRaw / maxRange;
            let yNorm = yRaw / maxRange;
            // 微调让爱心更居中且正立 (不需要翻转，直接使用)
            return { x: xNorm, y: yNorm };
        }
        
        // 生成爱心上的文字点位 (数量自适应手机：根据屏幕宽度密度调整，250~350个，性能佳)
        function generateHeartTextPoints(densityFactor = 1.0) {
            const points = [];
            // 根据屏幕宽度设定文字数量: 手机屏幕小一点字太多会挤，但文字会自适应清晰 240~300左右既饱满又流畅
            let targetCount = Math.floor(260 * Math.min(1.2, Math.max(0.9, width / 400)));
            targetCount = Math.min(330, Math.max(200, targetCount));
            
            // 采样步长
            for (let i = 0; i < targetCount; i++) {
                const t = (i / targetCount) * Math.PI * 2;
                const heartCoord = getHeartPoint(t);
                // 为了爱心内部也填充得饱满，产生一些内部偏移点(让爱心看起来充实)
                // 80% 边缘点, 20% 内部随机微调点（但仍然在心形内部区域）
                let finalX = heartCoord.x;
                let finalY = heartCoord.y;
                const isInner = Math.random() < 0.22;  // 内部填充更丰富
                if (isInner) {
                    // 往心形内部收缩随机偏移，使爱心有厚度感
                    const shrink = 0.5 + Math.random() * 0.4;
                    finalX = heartCoord.x * shrink;
                    finalY = heartCoord.y * shrink;
                }
                points.push({ xNorm: finalX, yNorm: finalY, t });
            }
            // 额外增加心形中心区域的一些零散祝福 (增加密集温暖感)
            const extraCount = Math.floor(targetCount * 0.12);
            for (let i = 0; i < extraCount; i++) {
                const randAngle = Math.random() * Math.PI * 2;
                const heartCoord = getHeartPoint(randAngle);
                // 内部更靠近中心区域
                const innerFactor = 0.3 + Math.random() * 0.45;
                const xNorm = heartCoord.x * innerFactor;
                const yNorm = heartCoord.y * innerFactor;
                points.push({ xNorm, yNorm, t: randAngle });
            }
            return points;
        }
        
        // 更新画布尺寸 (适配手机屏幕及横竖屏)
        function resizeCanvas() {
            if (!canvas) return;
            const container = canvas.parentElement;
            width = window.innerWidth;
            height = window.innerHeight;
            canvas.width = width;
            canvas.height = height;
            canvas.style.width = `${width}px`;
            canvas.style.height = `${height}px`;
            if (ctx) {
                // 重绘前重新生成文字位置
                rebuildHeartTexts();
                drawHeart();
            }
        }
        
        // 重新构建文字队列：根据当前屏幕大小计算每个文字的位置、字体大小、独立色相偏移
        function rebuildHeartTexts() {
            if (!ctx || width === 0 || height === 0) return;
            
            // 生成标准化点集
            const points = generateHeartTextPoints();
            const newTextItems = [];
            
            // 爱心缩放变换: 占屏幕宽70%~75%，高60%左右，使得爱心完美居中并适配所有手机
            const scaleX = width * 0.72;
            const scaleY = height * 0.64;
            const offsetX = width / 2;
            const offsetY = height / 2 - height * 0.02;   // 稍微上提更美观
            
            for (let i = 0; i < points.length; i++) {
                const p = points[i];
                // 映射实际坐标
                let x = offsetX + p.xNorm * scaleX;
                let y = offsetY + p.yNorm * scaleY;
                // 边界裁剪保证不超出屏幕边缘太离谱，但基本都在视野内
                x = Math.min(Math.max(x, 22), width - 22);
                y = Math.min(Math.max(y, 48), height - 68);
                
                // 根据屏幕宽度动态设置字体大小: 大屏字大些，小屏字清晰且不拥挤 (0.82rem ~ 1.25rem)
                let baseFontSize = Math.max(13, Math.min(20, width / 27));
                // 根据爱心位置微调：心尖底部或边缘稍微小一点点但依然清晰
                let fontSize = baseFontSize + (Math.sin(p.t) * 1.5);
                fontSize = Math.floor(fontSize);
                fontSize = Math.min(24, Math.max(14, fontSize));
                
                // 随机生成祝福文本
                const blessingText = getRandomBlessing();
                
                // 每个文字独立色相偏移 (0~360)
                const hueOffset = Math.random() * 360;
                // 颜色变化速度系数 (让每个字闪烁频率不同)
                const colorSpeed = 0.002 + Math.random() * 0.003;
                // 微旋转角度让爱心生动
                const rotateDeg = (Math.random() - 0.5) * 6;
                
                newTextItems.push({
                    text: blessingText,
                    x: x,
                    y: y,
                    fontSize: fontSize,
                    hueOffset: hueOffset,
                    colorSpeed: colorSpeed,
                    rotate: rotateDeg,
                    alpha: 0.96,
                    // 用于记录相位飘移
                    phaseShift: Math.random() * Math.PI * 2
                });
            }
            
            // 重新赋值
            textItems = newTextItems;
        }
        
        // 绘制整个爱心 + 五颜六色动态文字 (每一帧颜色都变化，炫彩)
        let timeOffsetGlobal = 0;
        let lastTimestamp = 0;
        
        function drawHeart() {
            if (!ctx || width === 0 || height === 0) return;
            // 清空画布 — 透明背景保留渐变底层光晕，增强可读性
            ctx.clearRect(0, 0, width, height);
            
            // 1. 绘制梦幻背景柔光 (让文字更突出，爱心整体闪耀)
            const grad = ctx.createLinearGradient(0, 0, width, height);
            grad.addColorStop(0, '#0e0a1a');
            grad.addColorStop(0.5, '#1f1530');
            grad.addColorStop(1, '#120c1c');
            ctx.fillStyle = grad;
            ctx.fillRect(0, 0, width, height);
            
            // 增加爱心周围光晕粒子效果: 轻柔的发光层 (画一个半透明爱心光晕)
            ctx.save();
            ctx.shadowBlur = 0;
            const centerX = width/2;
            const centerY = height/2 - height*0.02;
            // 绘制心形光晕 (辅助提升浪漫感)
            ctx.beginPath();
            for (let t = 0; t <= Math.PI * 2; t += 0.05) {
                const heart = getHeartPoint(t);
                const xGlow = centerX + heart.x * (width * 0.74);
                const yGlow = centerY + heart.y * (height * 0.64);
                if (t === 0) ctx.moveTo(xGlow, yGlow);
                else ctx.lineTo(xGlow, yGlow);
            }
            ctx.closePath();
            ctx.fillStyle = 'rgba(255, 70, 130, 0.08)';
            ctx.fill();
            ctx.shadowBlur = 0;
            
            // 2. 绘制所有爱心文字 (确保文字清晰，开启文字优化)
            ctx.textAlign = "center";
            ctx.textBaseline = "middle";
            
            // 获取当前时间戳 (毫秒) 用于颜色动态变化
            const now = performance.now();
            timeOffsetGlobal = now * 0.0025;
            
            for (let i = 0; i < textItems.length; i++) {
                const item = textItems[i];
                // 动态炫彩颜色: HSL 色相随时间流动 + 每个字独立偏移，达到五彩斑斓变化
                let hue = (item.hueOffset + now * item.colorSpeed) % 360;
                // 饱和度和亮度保持鲜明但舒适: 饱和度 75%～95%, 亮度 60%～82%
                const sat = 72 + Math.sin(now * 0.002 + item.phaseShift) * 18;
                const light = 62 + Math.cos(now * 0.0025 + item.hueOffset * 0.02) * 15;
                const color = `hsl(${hue}, ${sat}%, ${light}%)`;
                
                // 设置字体样式: 加粗，使用系统字体以保证清晰
                ctx.font = `600 ${item.fontSize}px "PingFang SC", "Microsoft YaHei", system-ui, -apple-system, "Segoe UI", Roboto, Helvetica, Arial`;
                ctx.shadowBlur = 0;
                // 绘制文字阴影 (闪闪发光效果)
                ctx.shadowColor = `hsla(${hue}, 85%, 65%, 0.7)`;
                ctx.shadowBlur = 8;
                ctx.fillStyle = color;
                
                // 保存上下文并应用旋转
                ctx.save();
                ctx.translate(item.x, item.y);
                ctx.rotate(item.rotate * Math.PI / 180);
                // 轻微文字浮动效果 (增加呼吸感)
                const floatY = Math.sin(now * 0.003 + i) * 1.2;
                ctx.fillText(item.text, 0, floatY);
                ctx.restore();
                
                // 额外增加一小圈光晕 (第二次绘制更亮的边缘，加强闪闪发光)
                ctx.save();
                ctx.translate(item.x, item.y);
                ctx.rotate(item.rotate * Math.PI / 180);
                ctx.shadowBlur = 12;
                ctx.shadowColor = `hsla(${hue}, 85%, 70%, 0.5)`;
                ctx.fillStyle = `hsla(${hue}, 85%, 70%, 0.35)`;
                ctx.fillText(item.text, 0, floatY);
                ctx.restore();
            }
            
            // 额外增加小星星光点围绕爱心 (性能友好，增加华丽感)
            ctx.shadowBlur = 0;
            for (let s = 0; s < 80; s++) {
                const starT = (now * 0.002 + s) % (Math.PI * 2);
                const heartPos = getHeartPoint(starT);
                const starX = centerX + heartPos.x * (width * 0.75);
                const starY = centerY + heartPos.y * (height * 0.64);
                const starSize = 2 + Math.sin(now * 0.008 + s) * 1.2;
                ctx.beginPath();
                ctx.arc(starX, starY, starSize, 0, Math.PI * 2);
                ctx.fillStyle = `hsla(${(now * 2 + s * 15) % 360}, 85%, 70%, 0.5)`;
                ctx.fill();
            }
        }
        
        // 动画循环: 不断刷新颜色和重绘 (使颜色闪闪发光连续变动)
        function animate() {
            if (!ctx || width === 0 || height === 0) {
                requestAnimationFrame(animate);
                return;
            }
            // 绘制爱心 + 动态变色文字
            drawHeart();
            requestAnimationFrame(animate);
        }
        
        // 定时刷新祝福语内容 (每隔一段时间替换部分祝福文字，让爱心充满新鲜感，但是保留爱心形态)
        let refreshInterval = null;
        function startTextRefresh() {
            if (refreshInterval) clearInterval(refreshInterval);
            refreshInterval = setInterval(() => {
                if (!textItems.length) return;
                // 每次随机替换 15% ~ 25% 的文字，让内容流动但爱心结构不变
                const replaceCount = Math.max(12, Math.floor(textItems.length * 0.18));
                for (let i = 0; i < replaceCount; i++) {
                    const randomIndex = Math.floor(Math.random() * textItems.length);
                    if (textItems[randomIndex]) {
                        const newBless = getRandomBlessing();
                        textItems[randomIndex].text = newBless;
                        // 同时微调一下色相偏移，使得新文字有新的色彩个性
                        textItems[randomIndex].hueOffset = Math.random() * 360;
                        textItems[randomIndex].colorSpeed = 0.002 + Math.random() * 0.0035;
                    }
                }
                // 同时也更新极少部分文字的旋转角度，保持活泼 (不影响清晰)
                for (let i = 0; i < replaceCount/2; i++) {
                    const idx = Math.floor(Math.random() * textItems.length);
                    if(textItems[idx]) textItems[idx].rotate = (Math.random() - 0.5) * 7;
                }
            }, 5200);
        }
        
        // 适配手机屏幕旋转与resize事件，重新构建爱心布局，保证爱心不颠倒且位置完美
        function handleResize() {
            if (resizeTimer) clearTimeout(resizeTimer);
            resizeTimer = setTimeout(() => {
                resizeCanvas();
            }, 180);
        }
        
        // 初始化canvas及事件
        function init() {
            if (!canvas) return;
            ctx = canvas.getContext('2d');
            if (!ctx) return;
            
            resizeCanvas();
            // 生成文字列表后开始动画
            if (textItems.length === 0) {
                rebuildHeartTexts();
            }
            // 启动动画渲染
            animate();
            // 启动动态祝福语刷新
            startTextRefresh();
            
            window.addEventListener('resize', handleResize);
            window.addEventListener('orientationchange', handleResize);
        }
        
        // 保险：当页面完全加载后启动
        if (document.readyState === 'loading') {
            document.addEventListener('DOMContentLoaded', init);
        } else {
            init();
        }
        
        // 清理定时器 (可选，但不是必须)
        window.addEventListener('beforeunload', () => {
            if (refreshInterval) clearInterval(refreshInterval);
            if (resizeTimer) clearTimeout(resizeTimer);
            if (animationId) cancelAnimationFrame(animationId);
        });
        
        // 增加触感反馈（让用户知道爱心互动无卡顿，无额外损耗）
    })();
</script>
</body>
</html>
