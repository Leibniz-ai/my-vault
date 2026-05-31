# 年度展望

## 🏆 年度目标

- [ ] 
- [ ] 

```dataviewjs
// === 1. 月度导航控制台 ===
const year = dv.current().file.name;
const months = [
    { id: "01", name: "一月" }, { id: "02", name: "二月" }, { id: "03", name: "三月" },
    { id: "04", name: "四月" }, { id: "05", name: "五月" }, { id: "06", name: "六月" },
    { id: "07", name: "七月" }, { id: "08", name: "八月" }, { id: "09", name: "九月" },
    { id: "10", name: "十月" }, { id: "11", name: "十一月" }, { id: "12", name: "十二月" }
];

let html = `<div style="display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; margin-bottom: 20px;">`;

for (let m of months) {
    // ⚠️ 注意：这里指向 MyLife/Monthly 文件夹，确保你创建了这个文件夹
    let filename = `${year}-${m.id}`;
    let filepath = `MyLife/Monthly/${filename}`; 
    let page = dv.page(filepath);
    
    let color = "";
    let icon = "";
    
    if (page) {
        color = "background: var(--interactive-accent); color: var(--text-on-accent);";
        icon = "📂 已创建"; 
    } else {
        color = "background: var(--background-secondary); border: 1px dashed var(--text-muted); opacity: 0.8;";
        icon = "➕ 点击生成";
    }

    html += `
    <a class="internal-link" href="${filepath}" style="text-decoration: none;">
        <div style="padding: 12px; border-radius: 8px; text-align: center; ${color} transition: all 0.2s; cursor: pointer;">
            <div style="font-weight: bold; font-size: 1.2em;">${m.id}月</div>
            <div style="font-size: 0.8em; margin-top: 4px;">${icon}</div>
        </div>
    </a>
    `;
}
html += `</div>`;
dv.paragraph(html);
```
## 🗓️ 全年状态热力图

```dataviewjs
// 1. 获取年份
const currentYear = parseInt(dv.current().file.name); 

// 2. 表情定义
function getMoodEmoji(score) {
    if (!score && score !== 0) return "⚪"; 
    if (score >= 9) return "🤩";
    if (score >= 7) return "😊";
    if (score >= 5) return "😐";
    if (score >= 3) return "😞";
    return "😭";
}

// 3. 准备数据
let days = {}; 
for (let page of dv.pages('"MyLife/Daily"')) {
    if (!page.file.name.startsWith(currentYear)) continue;
    const score = page.score ?? null;
    days[page.file.name] = {
        emoji: getMoodEmoji(score),
        score: score,
        path: page.file.path,
        note: page.file.name
    };
}

// 4. 定义画单个月历的函数
function renderMonth(year, month) {
    const currentMonth = moment(`${year}-${String(month+1).padStart(2,'0')}`, "YYYY-MM");
    const totalDays = currentMonth.daysInMonth();
    let startDay = currentMonth.clone().startOf('month').day();
    if (startDay === 0) startDay = 7; 
    
    let html = `<div style='border:1px solid var(--background-modifier-border); border-radius:8px; padding:10px; background:var(--background-secondary);'>`;
    html += `<h3 style='text-align:center; margin:0 0 10px 0;'>${currentMonth.format("MMMM")}</h3>`;
    
    html += `<table style='width:100%; text-align:center; table-layout:fixed; font-size:0.8em; border-collapse:collapse;'>`;
    html += `<tr style='color:var(--text-muted)'><th>1</th><th>2</th><th>3</th><th>4</th><th>5</th><th>6</th><th>7</th></tr>`;
    
    let dayCount = 1;
    let rows = 6; 
    
    for (let r = 0; r < rows; r++) {
        if (dayCount > totalDays && r > 0) break; 
        html += "<tr>";
        for (let c = 1; c <= 7; c++) {
            if ((r === 0 && c < startDay) || dayCount > totalDays) {
                html += "<td></td>";
                continue;
            }
            
            let dateKey = `${year}-${String(month+1).padStart(2,'0')}-${String(dayCount).padStart(2,'0')}`;
            let data = days[dateKey];
            let targetPath = `MyLife/Daily/${dateKey}`;
            
            let bgStyle = "";
            let cellContent = "";
            
            // ✨ 修复：改回了居中对齐，去掉了 padding-left
            // width: 100% 确保点击区域铺满整个格子
            let shiftStyle = "display:block; width:100%; text-align:center;"; 
            
            if (data) {
                // === 有日记 (点击进入) ===
                if(data.score >= 8) bgStyle = "background:rgba(255, 165, 0, 0.2);"; 
                else if(data.score >= 1) bgStyle = "background:rgba(255, 165, 0, 0.05);";
                
                cellContent = `<a class="internal-link" href="${data.path}" 
                               style="text-decoration:none; color:inherit; ${shiftStyle}" 
                               title="${data.score}分 | 点击进入">${data.emoji}</a>`;
            } else {
                // === 无日记 (点击创建) ===
                cellContent = `<a class="internal-link" href="${targetPath}" 
                               style="text-decoration:none; color:var(--text-muted); opacity:0.3; ${shiftStyle}" 
                               title="点击创建 ${dateKey}">${dayCount}</a>`;
            }
            
            html += `<td style='padding:4px 0; border-radius:4px; ${bgStyle}'>${cellContent}</td>`;
            dayCount++;
        }
        html += "</tr>";
    }
    html += "</table></div>";
    return html;
}

// 5. 核心布局
let gridHtml = `<div style='display: grid; grid-template-columns: repeat(3, 1fr); gap: 20px;'>`;
for (let m = 0; m < 12; m++) {
    gridHtml += renderMonth(currentYear, m);
}
gridHtml += "</div>";

dv.paragraph(gridHtml);
```

```dataviewjs
// 1. 获取当前年份
const currentYear = parseInt(dv.current().file.name);
const nextYear = currentYear + 1;
const prevYear = currentYear - 1;

// 2. 定义路径 (请确保这和你之前建的 Yearly 文件夹一致)
// 如果你的年记直接放在根目录，就去掉 "MyLife/Yearly/"
const nextPath = `MyLife/Yearly/${nextYear}`;
const prevPath = `MyLife/Yearly/${prevYear}`;

// 3. 检查文件是否存在
const nextExists = dv.page(nextPath);
const prevExists = dv.page(prevPath);

// 4. 定义按钮样式
const btnStyle = "padding: 8px 16px; border-radius: 6px; text-decoration: none; font-weight: bold; transition: 0.2s;";
const activeColor = "background: var(--interactive-accent); color: var(--text-on-accent);";
const emptyColor = "background: var(--background-secondary); border: 1px dashed var(--text-muted); color: var(--text-muted);";

// 5. 生成 HTML
let html = `<div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px;">`;

// === 上一年按钮 ===
let prevStyle = prevExists ? activeColor : emptyColor;
let prevText = prevExists ? `⬅️ 回顾 ${prevYear}` : `📅 补全 ${prevYear}`;
html += `<a class="internal-link" href="${prevPath}" style="${btnStyle} ${prevStyle}">${prevText}</a>`;

// === 中间标题 ===
html += `<span style="font-weight:bold; opacity:0.5;">✨ ${currentYear} ✨</span>`;

// === 下一年按钮 ===
let nextStyle = nextExists ? activeColor : emptyColor;
let nextText = nextExists ? `展望 ${nextYear} ➡️` : `🚀 开启 ${nextYear}`;
html += `<a class="internal-link" href="${nextPath}" style="${btnStyle} ${nextStyle}">${nextText}</a>`;

html += `</div>`;

dv.paragraph(html);
```
