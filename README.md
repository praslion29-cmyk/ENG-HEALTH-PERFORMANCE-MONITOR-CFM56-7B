# ENG-HEALTH-PERFORMANCE-MONITOR-CFM56-7B

<html lang="en">

<head>

<meta charset="UTF-8">

<title>Fleet Engine Health Monitoring System</title>

<style>

body{
font-family:Arial;
background:#f4f6f7;
padding:20px;
}

h1{
color:#2c3e50;
}

input{
margin:4px;
padding:4px;
}

button{
padding:6px 12px;
margin-top:10px;
}

table{
border-collapse:collapse;
width:100%;
margin-top:20px;
background:white;
}

th,td{
border:1px solid #ccc;
padding:8px;
text-align:center;
}

th{
background:#2c3e50;
color:white;
}

.status-normal{
color:green;
font-weight:bold;
}

.status-monitor{
color:orange;
font-weight:bold;
}

.status-alert{
color:red;
font-weight:bold;
}

@media print{
button{display:none;}
input{border:none;}
}

</style>

</head>


<body>

<h1>✈ Fleet Engine Health Monitoring System</h1>

<h2>Flight Data Input</h2>

Tanggal
<input type="date" id="date"><br>

Aircraft Registration
<input type="text" id="reg" placeholder="PK-XXX"><br>

Engine Number
<input type="text" id="eng"><br><br>

EGT
<input type="number" id="egt">

N1
<input type="number" id="n1">

N2
<input type="number" id="n2"><br>

Oil Pressure
<input type="number" id="op">

Oil Temp
<input type="number" id="ot">

Vibration
<input type="number" step="0.1" id="vib">

Fuel Flow
<input type="number" id="ff"><br><br>

<button onclick="addRecord()">Add Fleet Record</button>

<button onclick="printReport()">Print Report</button>


<h2>Fleet Engine Status</h2>

<table id="fleetTable">

<thead>

<tr>

<th>Date</th>
<th>Aircraft</th>
<th>Engine</th>
<th>EGT</th>
<th>EGT Margin</th>
<th>N1</th>
<th>N2</th>
<th>Oil Pressure</th>
<th>Oil Temp</th>
<th>Vibration</th>
<th>Fuel Flow</th>
<th>Health Score</th>
<th>Status</th>
<th>Recommendation</th>
<th>Diagnosis</th>

</tr>

</thead>

<tbody></tbody>

</table>

<script>

const LIMITS={
EGT:950,
N1:5382,
N2:15183,
OilMin:13,
OilMax:60,
OilTemp:140,
Vibration:4,
FuelFlow:5160
};

function addRecord(){

let date=document.getElementById("date").value;
let reg=document.getElementById("reg").value;
let eng=document.getElementById("eng").value;

let egt=parseFloat(document.getElementById("egt").value);
let n1=parseFloat(document.getElementById("n1").value);
let n2=parseFloat(document.getElementById("n2").value);
let op=parseFloat(document.getElementById("op").value);
let ot=parseFloat(document.getElementById("ot").value);
let vib=parseFloat(document.getElementById("vib").value);
let ff=parseFloat(document.getElementById("ff").value);

let margin=LIMITS.EGT-egt;

let score=100;
let alerts=[];
let rec=[];
let diag=[];

if(egt>LIMITS.EGT){
alerts.push("EGT Overlimit");
rec.push("Immediate engine inspection");
score-=20;
}

if(n1>LIMITS.N1){
alerts.push("N1 Overlimit");
rec.push("Check engine overspeed event");
score-=15;
}

if(n2>LIMITS.N2){
alerts.push("N2 Overlimit");
rec.push("Inspect high pressure spool");
score-=15;
}

if(op<LIMITS.OilMin||op>LIMITS.OilMax){
alerts.push("Oil Pressure Abnormal");
rec.push("Inspect oil system and pump");
score-=15;
}

if(ot>LIMITS.OilTemp){
alerts.push("Oil Temp High");
rec.push("Check oil cooling system");
score-=10;
}

if(ff>LIMITS.FuelFlow){
alerts.push("Fuel Flow High");
rec.push("Engine wash or compressor inspection recommended");
score-=10;
}


if(vib>=1 && vib<=2){

alerts.push("Vibration Normal");

}

else if(vib>2 && vib<=3.5){

alerts.push("Vibration Monitor");
rec.push("Continue monitoring vibration trend");
score-=5;

}

else if(vib>3.5 && vib<=4){

alerts.push("Vibration Warning");
rec.push("Perform fan balance check");
diag.push("Fan imbalance suspected");
score-=10;

}

else if(vib>4){

alerts.push("Heavy Vibration Warning");
rec.push("Immediate engine inspection required");
diag.push("Severe rotor imbalance or bearing damage suspected");
score-=20;

}


if(egt>900 && ff>5000){

diag.push("Possible compressor fouling");

}

if(ot>135){

diag.push("Possible oil cooling problem");

}

let status="";
let cls="";

if(score>=90){

status="Normal";
cls="status-normal";

}

else if(score>=70){

status="Monitor";
cls="status-monitor";

}

else{

status="Maintenance Required";
cls="status-alert";

}

if(rec.length==0){rec.push("None")}
if(diag.length==0){diag.push("None")}

let row=`

<tr>

<td>${date}</td>
<td>${reg}</td>
<td>${eng}</td>
<td>${egt}</td>
<td>${margin}</td>
<td>${n1}</td>
<td>${n2}</td>
<td>${op}</td>
<td>${ot}</td>
<td>${vib}</td>
<td>${ff}</td>
<td>${score}%</td>
<td class="${cls}">${status}</td>
<td>${rec.join(", ")}</td>
<td>${diag.join(", ")}</td>

</tr>

`;

document.querySelector("#fleetTable tbody").innerHTML+=row;

}

function printReport(){

window.print();

}

</script>

