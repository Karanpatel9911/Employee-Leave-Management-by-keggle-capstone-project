/* CLOCK */

function updateClock(){

const now =
new Date().toLocaleTimeString();

const ids = [
"clock",
"employeeClock",
"adminClock"
];

ids.forEach(id=>{

const el =
document.getElementById(id);

if(el){

el.innerHTML = now;
}
});
}

setInterval(updateClock,1000);

updateClock();

/* STARS */

const stars =
document.getElementById("stars");

for(let i=0;i<150;i++){

let star =
document.createElement("div");

star.classList.add("star");

star.style.top =
Math.random()*100 + "%";

star.style.left =
Math.random()*100 + "%";

stars.appendChild(star);
}

/* LOGIN */

let currentRole = "";
let currentUser = "";

function openLogin(role){

currentRole = role;

document
.getElementById("loginPanel")
.style.display = "block";

const adminHint =
document.getElementById("adminDemoHint");

if(role === "admin"){

adminHint.classList.remove("hidden");

}else{

adminHint.classList.add("hidden");
}
}

/* ADMIN QUICK DEMO LOGIN */

function quickAdminLogin(){

document
.getElementById("loginUsername")
.value = "admin123";

document
.getElementById("loginPassword")
.value = "admin123";

loginUser();
}

function loginUser(){

const username =
document
.getElementById("loginUsername")
.value.trim();

const password =
document
.getElementById("loginPassword")
.value.trim();

if(username === "" ||
password === ""){

alert(
"Fill all fields"
);

return;
}

currentUser = username;

localStorage.setItem(
"currentUser",
currentUser
);

if(currentRole === "user"){

document
.getElementById("welcomePage")
.classList.add("hidden");

document
.getElementById("employeePage")
.classList.remove("hidden");

renderLeaves();
}

if(currentRole === "admin"){

document
.getElementById("welcomePage")
.classList.add("hidden");

document
.getElementById("adminPage")
.classList.remove("hidden");

renderLeaves();

renderAnalytics();

renderGlobalAuditLog();
}
}

/* MENU */

function toggleMenu(menuId){

document
.getElementById(menuId)
.classList.toggle("active");
}

/* LOGOUT */

function logout(){

document
.getElementById("employeePage")
.classList.add("hidden");

document
.getElementById("adminPage")
.classList.add("hidden");

document
.getElementById("welcomePage")
.classList.remove("hidden");

document
.getElementById("loginPanel")
.style.display = "none";

document
.getElementById("adminDemoHint")
.classList.add("hidden");

document
.getElementById("leaveForm")
.reset();
}

/* DATE */

const today =
new Date().toISOString().split("T")[0];

document
.getElementById("fromDate")
.min = today;

document
.getElementById("toDate")
.min = today;

document
.getElementById("fromDate")
.addEventListener("change",()=>{

document
.getElementById("toDate")
.min =
document
.getElementById("fromDate")
.value;
});

/* STORAGE */

let allLeaves =
JSON.parse(
localStorage.getItem("allLeaves")
) || [];

let auditLog =
JSON.parse(
localStorage.getItem("auditLog")
) || [];

/* ENTER NEXT FIELD */

const fields =
document.querySelectorAll(
"#leaveForm input, #leaveForm textarea, #leaveForm select"
);

fields.forEach((field,index)=>{

field.addEventListener("keydown",(e)=>{

if(e.key === "Enter"){

e.preventDefault();

if(fields[index + 1]){

fields[index + 1].focus();
}
}
});
});

/* =========================================================
   AGENT 1: LEAVE VALIDATION AGENT
   - Checks the leave request and produces a confidence
     score + explainable reasons (Explainable AI feature)
   ========================================================= */

function leaveValidationAgent(leave, existingLeaves){

let score = 100;

const reasons = [];

const fromDate = new Date(leave.fromDate);

const toDate = new Date(leave.toDate);

const days =
Math.round(
(toDate - fromDate) /
(1000*60*60*24)
) + 1;

/* Duration check */

if(days > 15){

score -= 25;

reasons.push(
"⚠️ Long duration (" + days + " days) reduces confidence"
);

}else{

reasons.push(
"✅ Duration of " + days + " day(s) is within normal range"
);
}

/* Overlap check with same user's existing leaves */

const overlap =
existingLeaves.some(l=>

l.username === leave.username &&

l.status !== "rejected" &&

l.status !== "cancelled" &&

(leave.fromDate <= l.toDate &&
leave.toDate >= l.fromDate)
);

if(overlap){

score -= 35;

reasons.push(
"⚠️ Overlaps with another existing leave request"
);

}else{

reasons.push(
"✅ No overlap with existing leave records"
);
}

/* Reason-type keyword match (basic NLP style check) */

const keywordMap = {

"Medical Leave":["sick","fever","hospital","doctor","health","medical","ill"],

"Family Function":["wedding","marriage","family","function","ceremony","festival"],

"Urgent Work":["urgent","work","office","client","deadline","meeting"],

"Personal Work":["personal","bank","home","document","work"]
};

const keywords =
keywordMap[leave.leaveType] || [];

const reasonLower =
leave.reason.toLowerCase();

const matched =
keywords.some(k=>reasonLower.includes(k));

if(matched){

score += 5;

reasons.push(
"✅ Reason text matches the selected leave type"
);

}else{

score -= 10;

reasons.push(
"⚠️ Reason text doesn't clearly match selected leave type"
);
}

/* Frequency check - too many requests this month */

const sameMonthCount =
existingLeaves.filter(l=>

l.username === leave.username &&

new Date(l.fromDate).getMonth() ===
fromDate.getMonth()

).length;

if(sameMonthCount >= 3){

score -= 15;

reasons.push(
"⚠️ This is the " + (sameMonthCount + 1) +
"th request this month for this employee"
);
}

score = Math.max(0,Math.min(100,score));

const valid = score >= 40;

return {

score,

valid,

reasons,

days
};
}

/* =========================================================
   AGENT 2: APPROVAL RECOMMENDATION AGENT
   - Suggests Approve / Review / Reject with confidence
   - Final decision is ALWAYS taken by Admin (Human-in-the-Loop)
   ========================================================= */

function approvalRecommendationAgent(leave, validation){

let score = validation.score;

const reasons = [
...validation.reasons
];

/* Leave type priority weighting */

const priorityBoost = {

"Medical Leave":10,

"Family Function":5,

"Urgent Work":0,

"Personal Work":-5
};

score += priorityBoost[leave.leaveType] || 0;

if(priorityBoost[leave.leaveType] > 0){

reasons.push(
"✅ " + leave.leaveType +
" is treated as a higher priority category"
);
}

score = Math.max(0,Math.min(100,score));

let decision = "Review";

if(score >= 75){

decision = "Approve";

}else if(score < 45){

decision = "Reject";
}

reasons.push(
"🤖 Final recommendation computed at " + score + "% confidence"
);

return {

decision,

score,

reasons
};
}

/* =========================================================
   FEATURE: AUDIT TRAIL
   Every action gets logged on the leave object AND in a
   global audit log used by the Admin Audit Log panel.
   ========================================================= */

function logAudit(leave,action,by,details){

const entry = {

time:new Date().toLocaleString(),

action,

by,

details:details || "",

leaveId:leave ? leave.id : null,

employee:leave ? leave.name : ""
};

if(leave){

if(!leave.auditTrail){

leave.auditTrail = [];
}

leave.auditTrail.push(entry);
}

auditLog.unshift(entry);

localStorage.setItem(
"auditLog",
JSON.stringify(auditLog)
);
}

/* FORM */

const form =
document.getElementById("leaveForm");

form.addEventListener("submit",(e)=>{

e.preventDefault();

const name =
document.getElementById("name").value.trim();

const leaveType =
document.getElementById("leaveType").value;

const reason =
document.getElementById("reason").value.trim();

const fromDate =
document.getElementById("fromDate").value;

const toDate =
document.getElementById("toDate").value;

/* VALIDATION */

const textPattern =
/^[A-Za-z\s]+$/;

if(!textPattern.test(name)){

alert(
"Only characters allowed in name"
);

return;
}

if(!textPattern.test(reason)){

alert(
"Only characters allowed in reason"
);

return;
}

if(
name === "" ||
leaveType === "" ||
reason === "" ||
fromDate === "" ||
toDate === ""
){

alert(
"Fill all fields"
);

return;
}

if(toDate < fromDate){

alert(
"Invalid To Date"
);

return;
}

/* SAVE */

const leave = {

id:Date.now(),

username:currentUser,

name,
leaveType,
reason,
fromDate,
toDate,

status:"pending",

auditTrail:[]
};

/* RUN AGENT 1: VALIDATION */

const validation =
leaveValidationAgent(leave,allLeaves);

leave.validation = validation;

/* RUN AGENT 2: APPROVAL RECOMMENDATION */

const recommendation =
approvalRecommendationAgent(leave,validation);

leave.recommendation = recommendation;

logAudit(
leave,
"Submitted",
currentUser,
"Leave applied. AI validation score: " +
validation.score +
"%, recommendation: " +
recommendation.decision
);

allLeaves.push(leave);

saveData();

form.reset();

alert(
"✅ Leave Applied Successfully\n\n" +
"🤖 AI Validation Confidence: " + validation.score + "%\n" +
"🤖 AI Recommendation: " + recommendation.decision +
" (" + recommendation.score + "% confidence)\n\n" +
"Final decision will be taken by Admin."
);
});

/* SAVE */

function saveData(){

localStorage.setItem(
"allLeaves",
JSON.stringify(allLeaves)
);

renderLeaves();

renderAnalytics();

renderGlobalAuditLog();
}

/* =========================================================
   UI HELPERS: confidence bar + audit trail HTML builders
   ========================================================= */

function confidenceBarHTML(score,label){

return `
<div class="confidence-wrap">
<div class="confidence-label">
<span>${label}</span>
<span>${score}%</span>
</div>
<div class="confidence-track">
<div class="confidence-fill" style="width:${score}%"></div>
</div>
</div>
`;
}

function reasonsListHTML(reasons){

return `<ul>${
reasons.map(r=>`<li>${r}</li>`).join("")
}</ul>`;
}

function auditTrailHTML(leave){

if(!leave.auditTrail || leave.auditTrail.length === 0){

return `<p style="font-size:12px;opacity:0.6;">No audit history yet.</p>`;
}

return `
<div class="audit-trail">
${
leave.auditTrail.map(entry=>`
<div class="audit-entry">
<b>${entry.action}</b> by ${entry.by} — ${entry.time}
${entry.details ? "<br>" + entry.details : ""}
</div>
`).join("")
}
</div>
`;
}

function decisionBadgeClass(decision){

if(decision === "Approve") return "decision-approve";

if(decision === "Reject") return "decision-reject";

return "decision-review";
}

/* TOGGLE AI / AUDIT PANELS ON A CARD */

function toggleAIPanel(panelId){

const panel =
document.getElementById(panelId);

if(panel){

panel.classList.toggle("active");
}
}

/* RENDER */

function renderLeaves(){

const leaveList =
document.getElementById("leaveList");

const adminLeaveList =
document.getElementById("adminLeaveList");

if(leaveList) leaveList.innerHTML = "";

if(adminLeaveList) adminLeaveList.innerHTML = "";

/* USER */

if(leaveList){

const userLeaves =
allLeaves.filter(
leave=>leave.username === currentUser
);

userLeaves.forEach((leave)=>{

const card =
document.createElement("div");

card.classList.add("leave-card");

const validation =
leave.validation ||
{score:0,reasons:["No data"]};

const aiPanelId =
"aiPanel-" + leave.id;

const auditPanelId =
"auditPanel-" + leave.id;

card.innerHTML = `

<p>
👤 <strong>Name:</strong>
${leave.name}
</p>

<p>
📋 <strong>Leave Type:</strong>
${leave.leaveType}
</p>

<p>
📝 <strong>Reason:</strong>
${leave.reason}
</p>

<p>
📅 <strong>Duration:</strong>
${leave.fromDate} → ${leave.toDate}
</p>

<div class="status ${leave.status}">
${leave.status.toUpperCase()}
</div>

<div class="ai-toggle-row">

<button type="button" class="ai-toggle-btn"
onclick="toggleAIPanel('${aiPanelId}')">

🤖 AI Insights (${validation.score}%)

</button>

<button type="button" class="ai-toggle-btn"
onclick="toggleAIPanel('${auditPanelId}')">

🧾 Audit Trail

</button>

</div>

<div class="ai-panel" id="${aiPanelId}">

<h4>Leave Validation Agent</h4>

${confidenceBarHTML(validation.score,"Validation Confidence")}

${reasonsListHTML(validation.reasons)}

</div>

<div class="ai-panel" id="${auditPanelId}">

<h4>Audit Trail</h4>

${auditTrailHTML(leave)}

</div>

${
leave.status === "pending"

?

`

<div class="action-buttons">

<button class="cancel-btn"
onclick="cancelLeave(${leave.id})">

❌ Cancel

</button>

</div>

`

:

""
}
`;

leaveList.appendChild(card);
});
}

/* ADMIN */

if(adminLeaveList){

allLeaves.forEach((leave)=>{

const adminCard =
document.createElement("div");

adminCard.classList.add("leave-card");

const recommendation =
leave.recommendation ||
{decision:"Review",score:0,reasons:["No data"]};

const auditPanelId =
"adminAuditPanel-" + leave.id;

const overrideTag =
(leave.status === "approved" &&
recommendation.decision !== "Approve") ||
(leave.status === "rejected" &&
recommendation.decision !== "Reject")

?
`<span class="override-tag">Human Override</span>`
:
"";

adminCard.innerHTML = `

<p>
👤 <strong>User:</strong>
${leave.username}
</p>

<p>
📋 <strong>Leave Type:</strong>
${leave.leaveType}
</p>

<p>
📝 <strong>Reason:</strong>
${leave.reason}
</p>

<p>
📅 <strong>Duration:</strong>
${leave.fromDate} → ${leave.toDate}
</p>

<div class="status ${leave.status}">
${leave.status.toUpperCase()}
</div>

${overrideTag}

<div class="ai-panel active">

<h4>🤖 Approval Recommendation Agent</h4>

<div class="decision-badge ${decisionBadgeClass(recommendation.decision)}">
Suggested: ${recommendation.decision}
</div>

${confidenceBarHTML(recommendation.score,"Recommendation Confidence")}

${reasonsListHTML(recommendation.reasons)}

<p class="hil-note">
👨‍💼 Human-in-the-Loop: This is only an AI suggestion.
Final approval/rejection is always decided by Admin.
</p>

</div>

<div class="ai-toggle-row">

<button type="button" class="ai-toggle-btn"
onclick="toggleAIPanel('${auditPanelId}')">

🧾 Audit Trail

</button>

</div>

<div class="ai-panel" id="${auditPanelId}">

<h4>Audit Trail</h4>

${auditTrailHTML(leave)}

</div>

${
leave.status === "pending"

?

`

<div class="action-buttons">

<button class="approve-btn"
onclick="approveLeave(${leave.id})">

✅ Approve

</button>

<button class="reject-btn"
onclick="rejectLeave(${leave.id})">

❌ Reject

</button>

</div>

`

:

""
}
`;

adminLeaveList.appendChild(adminCard);
});
}
}

/* ACTIONS */

function cancelLeave(id){

const leave =
allLeaves.find(l=>l.id === id);

if(leave){

logAudit(
leave,
"Cancelled",
currentUser,
"Employee cancelled the pending request"
);
}

allLeaves =
allLeaves.filter(
leave=>leave.id !== id
);

saveData();
}

function approveLeave(id){

allLeaves.forEach((leave)=>{

if(leave.id === id){

leave.status = "approved";

const ai =
leave.recommendation ?
leave.recommendation.decision :
"N/A";

logAudit(
leave,
"Approved",
currentUser,
"Admin approved (AI suggested: " + ai + ")"
);
}
});

saveData();

alert(
"🎉 Leave Approved"
);
}

function rejectLeave(id){

allLeaves.forEach((leave)=>{

if(leave.id === id){

leave.status = "rejected";

const ai =
leave.recommendation ?
leave.recommendation.decision :
"N/A";

logAudit(
leave,
"Rejected",
currentUser,
"Admin rejected (AI suggested: " + ai + ")"
);
}
});

saveData();

alert(
"❌ Leave Rejected"
);
}

/* CLEAR */

function clearAllRequests(){

if(confirm(
"Delete all requests?"
)){

logAudit(
null,
"Cleared All",
currentUser,
"Admin cleared all leave requests"
);

allLeaves = [];

saveData();
}
}

/* =========================================================
   AGENT 3: ANALYTICS / REPORTING AGENT
   ========================================================= */

function renderAnalytics(){

const grid =
document.getElementById("analyticsGrid");

const typeChart =
document.getElementById("typeChart");

if(!grid || !typeChart) return;

const total = allLeaves.length;

const pending =
allLeaves.filter(l=>l.status === "pending").length;

const approved =
allLeaves.filter(l=>l.status === "approved").length;

const rejected =
allLeaves.filter(l=>l.status === "rejected").length;

const decided = approved + rejected;

const approvalRate =
decided > 0 ?
Math.round((approved/decided)*100) :
0;

const avgConfidence =
total > 0 ?

Math.round(
allLeaves.reduce((sum,l)=>

sum + (l.validation ? l.validation.score : 0),0
) / total

) : 0;

grid.innerHTML = `

<div class="analytics-card">
<div class="value">${total}</div>
<div class="label">Total Requests</div>
</div>

<div class="analytics-card">
<div class="value">${pending}</div>
<div class="label">Pending</div>
</div>

<div class="analytics-card">
<div class="value">${approved}</div>
<div class="label">Approved</div>
</div>

<div class="analytics-card">
<div class="value">${rejected}</div>
<div class="label">Rejected</div>
</div>

<div class="analytics-card">
<div class="value">${approvalRate}%</div>
<div class="label">Approval Rate</div>
</div>

<div class="analytics-card">
<div class="value">${avgConfidence}%</div>
<div class="label">Avg AI Confidence</div>
</div>
`;

/* Leave type distribution bar chart */

const types = [
"Medical Leave",
"Urgent Work",
"Family Function",
"Personal Work"
];

const maxCount =
Math.max(
1,
...types.map(t=>

allLeaves.filter(l=>l.leaveType === t).length
)
);

typeChart.innerHTML =
types.map(t=>{

const count =
allLeaves.filter(l=>l.leaveType === t).length;

const pct =
Math.round((count/maxCount)*100);

return `
<div class="bar-row">
<div class="bar-label">${t}</div>
<div class="bar-track">
<div class="bar-fill" style="width:${pct}%"></div>
</div>
<div class="bar-count">${count}</div>
</div>
`;
}).join("");
}

function toggleAnalytics(){

const panel =
document.getElementById("analyticsPanel");

if(panel){

panel.classList.toggle("hidden");

renderAnalytics();
}
}

/* GLOBAL AUDIT LOG PANEL */

function renderGlobalAuditLog(){

const list =
document.getElementById("globalAuditList");

if(!list) return;

if(auditLog.length === 0){

list.innerHTML =
`<p style="opacity:0.6;font-size:13px;">No audit entries yet.</p>`;

return;
}

list.innerHTML =
auditLog.map(entry=>`

<div class="audit-log-row">
<div class="time">${entry.time}</div>
<strong>${entry.action}</strong> — ${entry.employee || "System"}
by <b>${entry.by}</b>
${entry.details ? "<br>" + entry.details : ""}
</div>

`).join("");
}

function toggleAuditLog(){

const panel =
document.getElementById("auditLogPanel");

if(panel){

panel.classList.toggle("hidden");

renderGlobalAuditLog();
}
}

/* INITIAL */

renderLeaves();
