<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>徐烨 · 星光祝福</title>
<style>
    * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
        user-select: none;
        -webkit-tap-highlight-color: transparent;
    }

    body {
        min-height: 100vh;
        background: radial-gradient(circle at 20% 30%, #0a0f1e, #03060c);
        overflow-x: hidden;
        font-family: "PingFang SC", "Microsoft YaHei", "Helvetica Neue", system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
        position: relative;
        overflow: hidden;
        touch-action: none;
    }

    /* 闪闪发光的星星背景粒子 (第一个版本的华丽感) */
    .star-bg {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        pointer-events: none;
        z-index: 1;
        overflow: hidden;
    }
    
    .star {
        position: absolute;
        background: radial-gradient(circle, rgba(255,255,200,0.9) 0%, rgba(255,215,0,0.4) 100%);
        border-radius: 50%;
        animation: twinkle 3s ease-in-out infinite;
        box-shadow: 0 0 8px rgba(255,215,0,0.6);
    }
    
    @keyframes twinkle {
        0% { opacity: 0.3; transform: scale(1); }
        50% { opacity: 1; transform: scale(1.2); }
        100% { opacity: 0.3; transform: scale(1); }
    }

    /* 标题区域：闪闪发光的标题 (保留第一个版本的华丽感) */
    .hero {
        text-align: center;
        padding: 2rem 1rem;
        position: relative;
        z-index: 15;
        backdrop-filter: blur(3px);
        pointer-events: none;
    }

    .glow-title {
        font-size: 2rem;
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

    /* 漂浮文字容器 */
    .floating-container {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        pointer-events: none;
        z-index: 20;
        overflow: hidden;
    }

    /* 单个祝福词样式 — 每个字固定不同颜色，慢慢出现再消失 */
    .bless-word {
        position: absolute;
        white-space: nowrap;
        font-weight: 700;
        pointer-events: none;
        will-change: transform, opacity;
        font-family: inherit;
        letter-spacing: 1px;
        opacity: 0;
        filter: drop-shadow(0 0 6px currentColor);
        text-shadow: 0 0 8px rgba(255,255,180,0.7);
        animation: fadeInOutGentle 4.2s ease-in-out forwards;
        z-index: 25;
        font-size: 1.1rem;
    }
    
    /* 出现 -> 停留 -> 消失 (速度适中，温柔) */
    @keyframes fadeInOutGentle {
        0% {
            opacity: 0;
            transform: translateY(0px) scale(0.9);
        }
        18% {
            opacity: 1;
            transform: translateY(-8px) scale(1.05);
        }
        75% {
            opacity: 1;
            transform: translateY(-18px) scale(1);
        }
        100% {
            opacity: 0;
            transform: translateY(-32px) scale(0.92);
        }
    }

    /* 底部浪漫提示 */
    .info-tip {
        position: fixed;
        bottom: 20px;
        left: 0;
        right: 0;
        text-align: center;
        font-size: 12px;
        color: #b9b27e;
        background: rgba(0,0,0,0.45);
        width: fit-content;
        margin: 0 auto;
        padding: 6px 18px;
        border-radius: 60px;
        backdrop-filter: blur(12px);
        z-index: 30;
        pointer-events: none;
        font-family: monospace;
        letter-spacing: 1px;
    }

    /* 手机适配 */
    @media (max-width: 600px) {
        .glow-title {
            font-size: 1.5rem;
        }
        .bless-word {
            font-size: 0.92rem;
        }
        .sub {
            font-size: 0.7rem;
        }
    }
    
    @media (max-width: 450px) {
        .bless-word {
            font-size: 0.85rem;
        }
        .glow-title {
            font-size: 1.3rem;
        }
    }
    
    /* 闪光小粒子背景动画 */
    .shimmer-overlay {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background: radial-gradient(ellipse at 30% 40%, rgba(255,200,100,0.08) 0%, transparent 70%);
        pointer-events: none;
        z-index: 2;
        animation: bgPulse 5s infinite alternate;
    }
    
    @keyframes bgPulse {
        0% { opacity: 0.4; }
        100% { opacity: 1; }
    }
</style>
</head>
<body>

<!-- 第一个版本的华丽背景：闪烁星星 -->
<div class="star-bg" id="starBg"></div>
<div class="shimmer-overlay"></div>

<div class="hero">
    <div class="glow-title">✨ 徐烨 · 星光祝福 ✨</div>
    <div class="sub">∞ 每一句祝福 · 为你闪耀 ∞</div>
</div>

<div class="floating-container" id="floatContainer"></div>
<div class="info-tip">⚡ 五彩祝福 · 缓缓浮现又消散 ⚡</div>

<script>
    (function() {
        // ----------------------------- 祝福词库 (美好祝愿) -----------------------------
        const WISH_LIST = [
            "身体健康", "万事如意", "心想事成", "前程似锦", "笑口常开",
            "幸福美满", "平安喜乐", "财运亨通", "好运连连", "青春永驻",
            "事业腾飞", "甜蜜相伴", "星光璀璨", "梦想成真", "四季如春",
            "福气满满", "快乐无边", "温柔常驻", "光芒万丈", "未来可期",
            "朝气蓬勃", "顺遂无虞", "喜乐安宁", "明朗可爱", "花开富贵",
            "前程万里", "福星高照", "吉星高照", "如鱼得水", "龙马精神",
            "一生无忧", "良辰美景", "风和日丽", "甜蜜入梦", "温暖相伴"
        ];
        
        const NAME = "徐烨";
        
        // 生成完整祝福: "徐烨 · XXXX"
        function getRandomBlessing() {
            const randomIndex = Math.floor(Math.random() * WISH_LIST.length);
            const wish = WISH_LIST[randomIndex];
            // 偶尔加一颗星星，增加光彩感但不影响清晰度
            const decor = Math.random() > 0.8 ? " ✨" : (Math.random() > 0.85 ? " 💫" : "");
            return `${NAME} · ${wish}${decor}`;
        }
        
        // ---------- 固定颜色池 (每个字独立且鲜艳，在深色背景下闪耀，不再变化) ----------
        // 这些颜色都是高饱和、明亮、五彩斑斓
        const COLOR_PALETTE = [
            "#FF6B6B",  // 亮珊瑚红
            "#4ECDC4",  // 薄荷绿
            "#FFE66D",  // 柠檬黄
            "#FF9F4A",  // 暖橙
            "#BF7AF0",  // 淡紫罗兰
            "#70C8FF",  // 天湖蓝
            "#FF85B3",  // 樱花粉
            "#A2E3C4",  // 浅海绿
            "#FFB347",  // 橘黄
            "#D96CFF",  // 紫色
            "#5D9BEC",  // 亮蓝色
            "#F7C56E",  // 金盏黄
            "#FF8C94",  // 西瓜红
            "#86E3CE",  // 绿松石
            "#FFA07A",  // 亮橙
            "#C5A3FF",  // 浅紫
            "#F4A261",  // 杏色
            "#6CD4C5",  // 浅海蓝
            "#F08080",  // 淡珊瑚
            "#B5EAD7",  // 春绿
            "#FFD93D",  // 金色
            "#BB9AFF"   // 浅紫罗兰
        ];
        
        // 获取随机固定颜色
        function getRandomFixedColor() {
            const idx = Math.floor(Math.random() * COLOR_PALETTE.length);
            return COLOR_PALETTE[idx];
        }
        
        // 获取随机位置 (全屏范围内，避开边缘)
        function getRandomPosition() {
            const screenWidth = window.innerWidth;
            const screenHeight = window.innerHeight;
            const marginX = screenWidth * 0.06;
            const marginY = screenHeight * 0.12;  // 避开标题区域
            const maxX = screenWidth - marginX;
            const minX = marginX;
            const minY = marginY;
            const maxY = screenHeight - marginY * 1.1;
            
            const x = Math.random() * (maxX - minX) + minX;
            const y = Math.random() * (maxY - minY) + minY;
            return { x, y };
        }
        
        // 随机字体大小 (适配手机)
        function getRandomFontSize() {
            const base = window.innerWidth < 450 ? 0.85 : 0.95;
            return (base + Math.random() * 0.65) + 'rem';
        }
        
        // 随机旋转微小角度
        function getRandomRotation() {
            return (Math.random() - 0.5) * 9;
        }
        
        // 随机动画时长 (4s ~ 5.2s，速度适中)
        function getRandomDuration() {
            return (3.8 + Math.random() * 1.6) + 's';
        }
        
        // 随机延迟 (让文字错落有致)
        function getRandomDelay() {
            return (Math.random() * 1.4) + 's';
        }
        
        let container = document.getElementById('floatContainer');
        let activeCount = 0;
        let maxActive = 55;      // 同时最多55个文字，性能良好且画面丰富
        
        // 生成单个祝福语
        function createFloatingWord() {
            if (!container) return;
            
            // 控制最大数量，超过则温柔移除最早的一个
            if (activeCount >= maxActive) {
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
            
            // 随机位置
            const { x, y } = getRandomPosition();
            wordDiv.style.left = x + 'px';
            wordDiv.style.top = y + 'px';
            
            // 每个字固定不同颜色，取自缤纷色板
            const textColor = getRandomFixedColor();
            wordDiv.style.color = textColor;
            
            // 闪闪发光的文字阴影 (同色系)
            wordDiv.style.textShadow = `0 0 10px ${textColor}aa, 0 0 4px rgba(255,235,140,0.8)`;
            wordDiv.style.filter = `drop-shadow(0 0 5px ${textColor}80)`;
            
            // 字体大小
            wordDiv.style.fontSize = getRandomFontSize();
            // 轻微旋转
            const rotate = getRandomRotation();
            wordDiv.style.transform = `rotate(${rotate}deg)`;
            
            // 动画时长与延迟
            const duration = getRandomDuration();
            const delay = getRandomDelay();
            wordDiv.style.animation = `fadeInOutGentle ${duration} ease-in-out forwards`;
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
        
        // 生成器：速度适中，间隔约430ms生成一个
        let generateInterval = null;
        let isPageVisible = true;
        
        function startGenerator() {
            if (generateInterval) clearInterval(generateInterval);
            generateInterval = setInterval(() => {
                if (!isPageVisible) return;
                createFloatingWord();
                // 偶尔生成双倍祝福增加浪漫感 (概率20%)
                if (Math.random() < 0.2 && activeCount < maxActive - 1) {
                    setTimeout(() => {
                        createFloatingWord();
                    }, 60);
                }
            }, 430);
        }
        
        // 预先生成一些文字，避免画面空洞
        function seedInitialWords() {
            const seedCount = 22;
            for (let i = 0; i < seedCount; i++) {
                setTimeout(() => {
                    if (container) createFloatingWord();
                }, i * 65);
            }
        }
        
        // 页面可见性处理
        function handleVisibilityChange() {
            isPageVisible = !document.hidden;
        }
        
        // 屏幕旋转适配: 清空后重新生成位置适配新屏幕尺寸
        let resizeTimeout = null;
        function handleResize() {
            if (resizeTimeout) clearTimeout(resizeTimeout);
            resizeTimeout = setTimeout(() => {
                if (container) {
                    // 清空所有现有文字，避免位置错位
                    while (container.firstChild) {
                        container.removeChild(container.firstChild);
                    }
                    activeCount = 0;
                    // 重新生成文字适应新屏幕
                    seedInitialWords();
                }
            }, 200);
        }
        
        // 生成闪闪发光的背景星星 (第一个版本的星空效果)
        function createStars() {
            const starContainer = document.getElementById('starBg');
            if (!starContainer) return;
            const starCount = window.innerWidth < 600 ? 120 : 180;
            for (let i = 0; i < starCount; i++) {
                const star = document.createElement('div');
                star.classList.add('star');
                const size = 1 + Math.random() * 3;
                star.style.width = size + 'px';
                star.style.height = size + 'px';
                star.style.left = Math.random() * 100 + '%';
                star.style.top = Math.random() * 100 + '%';
                const duration = 1.5 + Math.random() * 3;
                star.style.animationDuration = duration + 's';
                star.style.animationDelay = Math.random() * 5 + 's';
                starContainer.appendChild(star);
            }
        }
        
        // 重新生成星星（屏幕旋转时可重新生成适合尺寸的星星，但不需要每次清空，简单重新调整位置）
        function refreshStars() {
            const starContainer = document.getElementById('starBg');
            if (starContainer) {
                while (starContainer.firstChild) {
                    starContainer.removeChild(starContainer.firstChild);
                }
                createStars();
            }
        }
        
        // 启动所有
        function init() {
            if (!container) return;
            createStars();
            startGenerator();
            seedInitialWords();
            document.addEventListener('visibilitychange', handleVisibilityChange);
            window.addEventListener('resize', () => {
                handleResize();
                refreshStars();
            });
            window.addEventListener('orientationchange', () => {
                setTimeout(() => {
                    refreshStars();
                }, 100);
            });
        }
        
        if (document.readyState === 'loading') {
            document.addEventListener('DOMContentLoaded', init);
        } else {
            init();
        }
        
        // 清理定时器
        window.addEventListener('beforeunload', () => {
            if (generateInterval) clearInterval(generateInterval);
            if (resizeTimeout) clearTimeout(resizeTimeout);
        });
        
        // 额外字体渲染优化
        const style = document.createElement('style');
        style.textContent = `
            .bless-word {
                font-weight: 650;
                -webkit-font-smoothing: antialiased;
                backface-visibility: hidden;
            }
        `;
        document.head.appendChild(style);
    })();
</script>
</body>
</html>
