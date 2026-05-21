<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>徐烨 · 浮现祝福</title>
<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
        -webkit-tap-highlight-color: transparent;
    }

    body {
        min-height: 100vh;
        background: linear-gradient(145deg, #0a0a1a 0%, #12102a 100%);
        font-family: "PingFang SC", "Microsoft YaHei", "Helvetica Neue", system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
        overflow: hidden;
        position: fixed;
        width: 100%;
        height: 100%;
        touch-action: none;
    }

    /* 容器占满全屏，文字绝对定位漂浮 */
    .floating-container {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        overflow: hidden;
        pointer-events: none;
        z-index: 10;
    }

    /* 单个祝福词样式 — 清晰、优雅 */
    .bless-word {
        position: absolute;
        white-space: nowrap;
        font-weight: 600;
        font-size: 1.1rem;
        pointer-events: none;
        will-change: transform, opacity;
        text-shadow: 0 0 6px rgba(255,255,200,0.5);
        font-family: inherit;
        letter-spacing: 1px;
        opacity: 0;
        /* 动画: 出现 -> 停留 -> 消失 */
        animation: fadeInOut 4.5s ease-in-out forwards;
        z-index: 20;
        filter: drop-shadow(0 2px 4px rgba(0,0,0,0.2));
    }

    /* 关键帧: 慢慢出现 -> 保持清晰 -> 慢慢消失 */
    @keyframes fadeInOut {
        0% {
            opacity: 0;
            transform: translateY(0px) scale(0.92);
        }
        15% {
            opacity: 1;
            transform: translateY(-6px) scale(1.02);
        }
        70% {
            opacity: 1;
            transform: translateY(-12px) scale(1);
        }
        100% {
            opacity: 0;
            transform: translateY(-25px) scale(0.96);
        }
    }

    /* 底部浪漫卡片 */
    .title-card {
        position: absolute;
        bottom: 8%;
        left: 50%;
        transform: translateX(-50%);
        background: rgba(0, 0, 0, 0.55);
        backdrop-filter: blur(20px);
        border-radius: 60px;
        padding: 10px 24px;
        text-align: center;
        z-index: 30;
        pointer-events: none;
        border: 1px solid rgba(255, 200, 150, 0.5);
        box-shadow: 0 0 28px rgba(255, 80, 120, 0.2);
        white-space: nowrap;
        max-width: 85vw;
    }

    .title-name {
        font-size: 1.4rem;
        font-weight: 800;
        background: linear-gradient(135deg, #FFD966, #FF85B3, #6BCBFF, #FFB347);
        background-size: 300% 300%;
        -webkit-background-clip: text;
        background-clip: text;
        color: transparent;
        animation: gradientFlow 3s ease infinite;
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
        margin-top: 5px;
        opacity: 0.85;
    }

    /* 顶部小提示 */
    .info-tip {
        position: absolute;
        top: 14px;
        left: 16px;
        background: rgba(0,0,0,0.4);
        backdrop-filter: blur(8px);
        padding: 4px 12px;
        border-radius: 40px;
        font-size: 10px;
        color: #ffcf9a;
        z-index: 30;
        font-family: monospace;
        pointer-events: none;
    }

    /* 手机适配字体大小 */
    @media (max-width: 550px) {
        .bless-word {
            font-size: 0.95rem;
        }
        .title-name {
            font-size: 1.2rem;
        }
    }
    @media (max-width: 400px) {
        .bless-word {
            font-size: 0.85rem;
        }
    }

    /* 让背景有呼吸感光晕 */
    .bg-glow {
        position: fixed;
        top: 50%;
        left: 50%;
        width: 120%;
        height: 120%;
        transform: translate(-50%, -50%);
        background: radial-gradient(circle, rgba(255,100,150,0.1) 0%, rgba(0,0,0,0) 70%);
        pointer-events: none;
        z-index: 0;
    }
</style>
</head>
<body>
<div class="bg-glow"></div>
<div class="floating-container" id="floatContainer"></div>
<div class="title-card">
    <div class="title-name">💖 徐烨 · 万千祝福 💖</div>
    <div class="sub-love">✨ 每一句都为你闪耀 ✨</div>
</div>
<div class="info-tip">❤️ 祝福浮现 · 慢慢消散 ❤️</div>

<script>
    (function() {
        // ----------------------------- 祝福词库 (吉祥话) -----------------------------
        const WISH_LIST = [
            "身体健康", "万事如意", "心想事成", "前程似锦", "笑口常开",
            "幸福美满", "平安喜乐", "财运亨通", "好运连连", "青春永驻",
            "事业腾飞", "甜蜜相伴", "星光璀璨", "梦想成真", "四季如春",
            "福气满满", "快乐无边", "温柔常驻", "光芒万丈", "未来可期",
            "朝气蓬勃", "顺遂无虞", "喜乐安宁", "明朗可爱", "花开富贵",
            "前程万里", "福星高照", "吉星高照", "如鱼得水", "龙马精神",
            "一生无忧", "良辰美景", "风和日丽", "心想事成", "甜蜜入梦"
        ];
        
        const NAME = "徐烨";
        
        // 生成完整的祝福语: "徐烨 · XXXX"
        function getRandomBlessing() {
            const randomIndex = Math.floor(Math.random() * WISH_LIST.length);
            const wish = WISH_LIST[randomIndex];
            // 随机加个小点缀但不会影响视觉干净
            const decor = Math.random() > 0.75 ? " ✨" : "";
            return `${NAME} · ${wish}${decor}`;
        }
        
        // ---------- 预定义一组明亮、清晰、高饱和且不同的颜色 (每个字随机从中选一个，不再动态变色) ----------
        // 这些颜色在深色背景上非常鲜艳且不刺眼
        const COLOR_PALETTE = [
            "#FF6B6B",  // 珊瑚红
            "#4ECDC4",  // 蒂芙尼绿
            "#FFE66D",  // 柠檬黄
            "#FF9F4A",  // 橙黄
            "#BF7AF0",  // 淡紫
            "#70C8FF",  // 天湖蓝
            "#FF85B3",  // 樱花粉
            "#A2E3C4",  // 薄荷
            "#FFB347",  // 橘色
            "#D96CFF",  // 紫罗兰
            "#5D9BEC",  // 亮蓝
            "#F7C56E",  // 金盏
            "#FF8C94",  // 西瓜红
            "#86E3CE",  // 绿松
            "#FFA07A",  // 亮橙
            "#C5A3FF",  // 浅紫罗兰
            "#F4A261",  // 暖杏
            "#6CD4C5",  // 浅海绿
            "#F08080",  // 淡珊瑚
            "#B5EAD7"   // 浅春绿
        ];
        
        // 获取随机颜色 (固定颜色，不随时间变化)
        function getRandomFixedColor() {
            const idx = Math.floor(Math.random() * COLOR_PALETTE.length);
            return COLOR_PALETTE[idx];
        }
        
        // 生成随机位置 (全屏范围内，避开边缘不舒适区域)
        function getRandomPosition() {
            const screenWidth = window.innerWidth;
            const screenHeight = window.innerHeight;
            // 左右留出 5% 边距，顶部留出 10% 避免遮住卡片
            const marginX = screenWidth * 0.05;
            const marginY = screenHeight * 0.1;
            const maxX = screenWidth - marginX;
            const minX = marginX;
            const minY = marginY;
            const maxY = screenHeight - marginY * 1.2;
            
            const x = Math.random() * (maxX - minX) + minX;
            const y = Math.random() * (maxY - minY) + minY;
            return { x, y };
        }
        
        // 随机字体大小 (手机适配 0.85rem ~ 1.5rem 之间，确保清晰)
        function getRandomFontSize() {
            const base = window.innerWidth < 450 ? 0.85 : 0.95;
            return (base + Math.random() * 0.6) + 'rem';
        }
        
        // 随机旋转微小角度，增加灵动感
        function getRandomRotation() {
            return (Math.random() - 0.5) * 8; // -4deg ~ 4deg
        }
        
        // 随机动画时长 (让消失节奏略有差异，但总体速度适中 4s ~ 5.2s)
        function getRandomDuration() {
            return (3.8 + Math.random() * 1.4) + 's';
        }
        
        // 随机延迟 (让文字错落出现，不拥挤)
        function getRandomDelay() {
            return (Math.random() * 1.2) + 's';
        }
        
        let container = document.getElementById('floatContainer');
        let activeCount = 0;
        let maxActive = 48;      // 同时最多出现48个文字，性能流畅
        
        // 生成单个祝福语并添加到容器中
        function createFloatingWord() {
            if (!container) return;
            // 控制同时存在的最大数量，避免过卡 (但手机完全无压力)
            if (activeCount >= maxActive) {
                // 如果超过上限，移除最早的一个文字 (温柔清理)
                const firstChild = container.firstChild;
                if (firstChild) {
                    container.removeChild(firstChild);
                    activeCount--;
                }
            }
            
            const blessing = getRandomBlessing();
            const wordDiv = document.createElement('div');
            wordDiv.className = 'bless-word';
            wordDiv.innerText = blessing;
            
            // 位置
            const { x, y } = getRandomPosition();
            wordDiv.style.left = x + 'px';
            wordDiv.style.top = y + 'px';
            
            // 固定颜色 (每一个字不同颜色，取自鲜艳调色板)
            const textColor = getRandomFixedColor();
            wordDiv.style.color = textColor;
            
            // 文字阴影使用同色系半透明，增加发光感且柔和
            wordDiv.style.textShadow = `0 0 8px ${textColor}80, 0 1px 2px rgba(0,0,0,0.3)`;
            
            // 字体大小
            wordDiv.style.fontSize = getRandomFontSize();
            // 旋转
            const rotate = getRandomRotation();
            wordDiv.style.transform = `rotate(${rotate}deg)`;
            // 动画时长与延迟
            const duration = getRandomDuration();
            const delay = getRandomDelay();
            wordDiv.style.animation = `fadeInOut ${duration} ease-in-out forwards`;
            wordDiv.style.animationDelay = delay;
            
            // 动画结束后移除元素
            const onAnimationEnd = () => {
                if (wordDiv && wordDiv.remove) {
                    wordDiv.remove();
                    activeCount--;
                }
                wordDiv.removeEventListener('animationend', onAnimationEnd);
            };
            wordDiv.addEventListener('animationend', onAnimationEnd);
            
            container.appendChild(wordDiv);
            activeCount++;
        }
        
        // 设定文字生成速度适中: 每秒大约 2~3 个新文字 (间隔 380ms 左右)
        let generateInterval = null;
        let isPageVisible = true;
        
        function startGenerator() {
            if (generateInterval) clearInterval(generateInterval);
            // 间隔 420ms 左右，速度舒适，文字不会过多拥挤
            generateInterval = setInterval(() => {
                if (!isPageVisible) return;
                // 每次生成一个文字
                createFloatingWord();
                // 偶尔有概率同时生成两个（增强丰富度但不至于卡）概率 25%，依然温和
                if (Math.random() < 0.25 && activeCount < maxActive - 1) {
                    setTimeout(() => {
                        createFloatingWord();
                    }, 50);
                }
            }, 420);
        }
        
        // 页面打开时立即预先生成一小批文字，让画面不空旷 (温和填充)
        function seedInitialWords() {
            const seedCount = 18;
            for (let i = 0; i < seedCount; i++) {
                setTimeout(() => {
                    if (container) createFloatingWord();
                }, i * 70);
            }
        }
        
        // 监听页面可见性，节省资源
        function handleVisibilityChange() {
            isPageVisible = !document.hidden;
            if (!isPageVisible) {
                // 页面隐藏时可选择不停止生成器，但依然保持不卡，无需额外操作，保留原生成即可
            }
        }
        
        // 监听屏幕旋转 / resize，需要重新调整？ 不需要重建文字，因为文字位置是随机固定的，旋转屏幕后现有文字位置可能偏移
        // 但为了更好的用户体验，当屏幕尺寸变化时，重新调整所有现有文字的位置？不必过于复杂，可以重新适配但会导致文字跳动。
        // 更好办法：屏幕旋转时清空现有文字，重新开始生成新适配大小的文字，并且适应新屏幕尺寸。
        let resizeTimeout = null;
        function handleResize() {
            if (resizeTimeout) clearTimeout(resizeTimeout);
            resizeTimeout = setTimeout(() => {
                // 清空所有现有文字，重新在新屏幕尺寸下生成一批文字
                if (container) {
                    // 移除所有孩子
                    while (container.firstChild) {
                        container.removeChild(container.firstChild);
                    }
                    activeCount = 0;
                    // 重新生成种子文字，让布局适应新宽高
                    seedInitialWords();
                }
            }, 200);
        }
        
        // 启动所有效果
        function init() {
            if (!container) return;
            startGenerator();
            seedInitialWords();
            document.addEventListener('visibilitychange', handleVisibilityChange);
            window.addEventListener('resize', handleResize);
            window.addEventListener('orientationchange', handleResize);
        }
        
        // 页面完全加载后启动
        if (document.readyState === 'loading') {
            document.addEventListener('DOMContentLoaded', init);
        } else {
            init();
        }
        
        // 清理定时器（可选，对页面关闭无影响）
        window.addEventListener('beforeunload', () => {
            if (generateInterval) clearInterval(generateInterval);
            if (resizeTimeout) clearTimeout(resizeTimeout);
        });
        
        // 为了保证字体在手机上极致清晰，添加额外样式强制抗锯齿
        const style = document.createElement('style');
        style.textContent = `
            .bless-word {
                font-weight: 600;
                -webkit-font-smoothing: antialiased;
                font-smoothing: antialiased;
                letter-spacing: 0.5px;
                backface-visibility: hidden;
            }
        `;
        document.head.appendChild(style);
    })();
</script>
</body>
</html>
