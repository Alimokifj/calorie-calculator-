<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<title>حاسبة السعرات والوزن المثالي</title>
<style>
body { font-family: Tahoma, sans-serif; background:#f0f4f8; color:#333; line-height:1.6; text-align:center; padding:20px;}
h1 { color:#2c3e50; margin-bottom:15px; }
.container { max-width:600px; margin:auto; background:#fff; padding:20px; border-radius:10px; box-shadow:0 0 15px rgba(0,0,0,0.1);}
label { display:block; text-align:right; margin-top:10px;}
input, select { width:100%; padding:10px; margin-top:5px; border:1px solid #ccc; border-radius:5px; font-size:15px;}
button { margin-top:15px; background:#27ae60; color:#fff; border:none; padding:12px; width:100%; border-radius:5px; cursor:pointer; font-size:16px;}
button:hover { background:#219150; }
.result { background:#ecf0f1; padding:15px; border-radius:8px; margin-top:20px; text-align:right; line-height:1.8;}
.note { font-size:13px; color:#666; margin-top:15px;}
</style>
</head>
<body>

<div class="container">
    <h1 id="title">📊 حاسبة السعرات والوزن المثالي</h1>

    <label id="labelLang">اللغة:</label>
    <select id="lang" onchange="changeLanguage()">
        <option value="ar">العربية</option>
        <option value="en">English</option>
    </select>

    <label id="labelGender">الجنس:</label>
    <select id="gender">
        <option value="male" data-ar="رجل" data-en="Male">رجل</option>
        <option value="female" data-ar="امرأة" data-en="Female">امرأة</option>
    </select>

    <label id="labelAge">العمر (بالسنوات):</label>
    <input type="number" id="age" placeholder="مثال: 25">

    <label id="labelWeight">الوزن (كغ):</label>
    <input type="number" id="weight" placeholder="مثال: 70">

    <label id="labelHeight">الطول (سم):</label>
    <input type="number" id="height" placeholder="مثال: 175">

    <label id="labelActivity">مستوى النشاط:</label>
    <select id="activity">
        <option value="1.2" data-ar="خامل (قليل الحركة)" data-en="Sedentary (little activity)">خامل (قليل الحركة)</option>
        <option value="1.375" data-ar="نشاط خفيف (تمارين بسيطة 1-3 أيام)" data-en="Light activity (1-3 days/week)">نشاط خفيف (تمارين بسيطة 1-3 أيام)</option>
        <option value="1.55" data-ar="نشاط متوسط (تمارين 3-5 أيام)" data-en="Moderate activity (3-5 days/week)">نشاط متوسط (تمارين 3-5 أيام)</option>
        <option value="1.725" data-ar="نشاط عالي (تمارين 6-7 أيام)" data-en="Very active (6-7 days/week)">نشاط عالي (تمارين 6-7 أيام)</option>
        <option value="1.9" data-ar="نشاط شديد جداً (تمارين مكثفة + عمل مجهد)" data-en="Extra active (intense exercise + labor)">نشاط شديد جداً (تمارين مكثفة + عمل مجهد)</option>
    </select>

    <button onclick="calculate()" id="calculateBtn">احسب الآن</button>

    <div class="result" id="output"></div>

    <div class="note" id="note">
        ⚠️ النتائج تقريبية وتعتمد على معادلات علمية شائعة (Mifflin-St Jeor + BMI + Body Fat + Macronutrients).  
        لا تعتبر خطة غذائية نهائية، استشر طبيب أو مختص تغذية قبل التغيير.
    </div>
</div>

<script>
// الترجمات
const translations = {
    ar: {
        title: "📊 حاسبة السعرات والوزن المثالي",
        labelLang: "اللغة:",
        labelGender: "الجنس:",
        labelAge: "العمر (بالسنوات):",
        labelWeight: "الوزن (كغ):",
        labelHeight: "الطول (سم):",
        labelActivity: "مستوى النشاط:",
        calculateBtn: "احسب الآن",
        note: "⚠️ النتائج تقريبية وتعتمد على معادلات علمية شائعة (Mifflin-St Jeor + BMI + Body Fat + Macronutrients). استشر طبيب أو مختص تغذية قبل التغيير.",
        bmiStatus: ["نقص في الوزن", "وزن طبيعي", "زيادة وزن", "سمنة"],
        bodyFatStatus: ["منخفضة جدًا", "صحية", "عالية", "مرتفعة جدًا"],
        calorieLabels: {
            BMR: "معدل الأيض الأساسي (BMR)",
            maintain: "السعرات المطلوبة للحفاظ على الوزن",
            lose: "لخسارة الوزن",
            gain: "لزيادة الوزن",
            idealWeight: "الوزن المثالي",
            BMI: "مؤشر كتلة الجسم (BMI)",
            bodyFat: "نسبة الدهون في الجسم",
            protein: "البروتين اليومي",
            carbs: "الكربوهيدرات اليومية",
            fat: "الدهون الصحية اليومية"
        },
        placeholders: { age:"مثال: 25", weight:"مثال: 70", height:"مثال: 175"},
        kcal: "سعرة/يوم",
        kg: "كغ",
        g:"غ"
    },
    en: {
        title: "📊 Calorie & Ideal Weight Calculator",
        labelLang: "Language:",
        labelGender: "Gender:",
        labelAge: "Age (years):",
        labelWeight: "Weight (kg):",
        labelHeight: "Height (cm):",
        labelActivity: "Activity level:",
        calculateBtn: "Calculate Now",
        note: "⚠️ Results are approximate based on scientific formulas (Mifflin-St Jeor + BMI + Body Fat + Macronutrients). Consult a doctor or nutritionist before making changes.",
        bmiStatus: ["Underweight", "Normal weight", "Overweight", "Obesity"],
        bodyFatStatus: ["Very Low", "Healthy", "High", "Very High"],
        calorieLabels: {
            BMR:"Basal Metabolic Rate (BMR)",
            maintain:"Calories to maintain weight",
            lose:"Calories to lose weight",
            gain:"Calories to gain weight",
            idealWeight:"Ideal weight",
            BMI:"Body Mass Index (BMI)",
            bodyFat:"Body Fat %",
            protein:"Daily Protein",
            carbs:"Daily Carbs",
            fat:"Daily Healthy Fats"
        },
        placeholders: { age:"Ex: 25", weight:"Ex: 70", height:"Ex: 175"},
        kcal:"kcal/day",
        kg:"kg",
        g:"g"
    }
};

function changeLanguage() {
    let lang=document.getElementById("lang").value;
    let t=translations[lang];
    document.getElementById("title").innerText=t.title;
    document.getElementById("labelLang").innerText=t.labelLang;
    document.getElementById("labelGender").innerText=t.labelGender;
    document.getElementById("labelAge").innerText=t.labelAge;
    document.getElementById("labelWeight").innerText=t.labelWeight;
    document.getElementById("labelHeight").innerText=t.labelHeight;
    document.getElementById("labelActivity").innerText=t.labelActivity;
    document.getElementById("calculateBtn").innerText=t.calculateBtn;
    document.getElementById("note").innerText=t.note;
    document.getElementById("age").placeholder=t.placeholders.age;
    document.getElementById("weight").placeholder=t.placeholders.weight;
    document.getElementById("height").placeholder=t.placeholders.height;

    let genderOptions=document.getElementById("gender").options;
    for(let i=0;i<genderOptions.length;i++){genderOptions[i].text=genderOptions[i].dataset[lang];}
    let activityOptions=document.getElementById("activity").options;
    for(let i=0;i<activityOptions.length;i++){activityOptions[i].text=activityOptions[i].dataset[lang];}

    if(document.getElementById("output").innerHTML.trim()!==""){calculate();}
    document.body.dir=lang==="ar"?"rtl":"ltr";
}

function calculate(){
    let gender=document.getElementById("gender").value;
    let age=parseFloat(document.getElementById("age").value);
    let weight=parseFloat(document.getElementById("weight").value);
    let height=parseFloat(document.getElementById("height").value);
    let activity=parseFloat(document.getElementById("activity").value);
    let lang=document.getElementById("lang").value;
    let t=translations[lang];

    if(isNaN(age)||isNaN(weight)||isNaN(height)){
        document.getElementById("output").innerHTML=lang==="ar"?"⚠️ الرجاء إدخال جميع القيم بشكل صحيح.":"⚠️ Please enter all values correctly.";
        return;
    }

    let BMR=(gender==="male")?(10*weight)+(6.25*height)-(5*age)+5:(10*weight)+(6.25*height)-(5*age)-161;
    let maintainCalories=BMR*activity;
    let loseCalories=maintainCalories-500;
    let gainCalories=maintainCalories+500;
    let idealWeight=22*((height/100)**2);
    let BMI=weight/((height/100)**2);
    let bmiStatus="";
    if(BMI<18.5) bmiStatus=t.bmiStatus[0];
    else if(BMI<25) bmiStatus=t.bmiStatus[1];
    else if(BMI<30) bmiStatus=t.bmiStatus[2];
    else bmiStatus=t.bmiStatus[3];

    let sexNum = (gender==="male")?1:0;
    let bodyFat = 1.2*BMI + 0.23*age - 10.8*sexNum - 5.4;
    let bfStatus = "";
    if((gender==="male" && bodyFat<6) || (gender==="female" && bodyFat<14)) bfStatus=t.bodyFatStatus[0];
    else if((gender==="male" && bodyFat<=24) || (gender==="female" && bodyFat<=31)) bfStatus=t.bodyFatStatus[1];
    else if((gender==="male" && bodyFat<=30) || (gender==="female" && bodyFat<=37)) bfStatus=t.bodyFatStatus[2];
    else bfStatus=t.bodyFatStatus[3];

    let proteinFactor, fatPercent;
    if(activity<=1.375){ proteinFactor=1.0; fatPercent=0.30; }
    else if(activity<=1.55){ proteinFactor=1.2; fatPercent=0.27; }
    else if(activity<=1.725){ proteinFactor=1.5; fatPercent=0.25; }
    else { proteinFactor=1.8; fatPercent=0.22; }

    let protein = weight * proteinFactor;
    let fatCalories = maintainCalories * fatPercent;
    let fat = fatCalories / 9;
    let carbs = (maintainCalories - (protein*4 + fatCalories)) / 4;

    document.getElementById("output").innerHTML=`
        🔹 ${t.calorieLabels.BMR}: <b>${BMR.toFixed(0)}</b> ${t.kcal}<br>
        🔹 ${t.calorieLabels.maintain}: <b>${maintainCalories.toFixed(0)}</b> ${t.kcal}<br>
        📉 ${t.calorieLabels.lose}: <b>${loseCalories.toFixed(0)}</b> ${t.kcal}<br>
        📈 ${t.calorieLabels.gain}: <b>${gainCalories.toFixed(0)}</b> ${t.kcal}<br>
        🔹 ${t.calorieLabels.idealWeight}: <b>${idealWeight.toFixed(1)}</b> ${t.kg}<br>
        🔹 ${t.calorieLabels.BMI}: <b>${BMI.toFixed(1)}</b> (${bmiStatus})<br>
        🔹 ${t.calorieLabels.bodyFat}: <b>${bodyFat.toFixed(1)}%</b> (${bfStatus})<br>
        🔹 ${t.calorieLabels.protein}: <b>${protein.toFixed(0)} ${t.g}</b><br>
        🔹 ${t.calorieLabels.carbs}: <b>${carbs.toFixed(0)} ${t.g}</b><br>
        🔹 ${t.calorieLabels.fat}: <b>${fat.toFixed(0)} ${t.g}</b>
    `;
}

changeLanguage();
</script>
</body>
</html>
