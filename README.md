# resume-analyzer-project  //http://127.0.0.1:5000

<!DOCTYPE html>
<html>
<head>
<title>AI Resume Analyzer</title>

<link href="https://fonts.googleapis.com/css2?family=Poppins&display=swap" rel="stylesheet">

<style>
body {
    font-family: 'Poppins', sans-serif;
    margin: 0;
    background: linear-gradient(135deg,#1e3c72,#2a5298);
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

/* Card */
.container {
    background: white;
    padding: 40px;
    width: 500px;
    border-radius: 20px;
    box-shadow: 0 20px 40px rgba(0,0,0,0.3);
    text-align: center;
}

/* Title */
h1 {
    margin-bottom: 20px;
}

/* Button */
button {
    background: linear-gradient(to right,#ff416c,#ff4b2b);
    color: white;
    padding: 12px 25px;
    border: none;
    border-radius: 30px;
    cursor: pointer;
}

button:hover {
    transform: scale(1.05);
}

/* Skills */
.skill {
    background: #2a5298;
    color: white;
    padding: 6px 12px;
    border-radius: 20px;
    margin: 5px;
    display: inline-block;
}

/* Cards */
.card {
    background: #f1f3f8;
    padding: 12px;
    border-radius: 10px;
    margin-top: 10px;
}

/* Progress */
.progress-bar {
    background: #ddd;
    border-radius: 20px;
}

.progress {
    height: 20px;
    color: white;
    text-align: center;
    border-radius: 20px;
}

/* Error */
.error {
    color: red;
}
</style>

</head>

<body>

<div class="container">

<h1>🚀 AI Resume Analyzer</h1>

<form method="POST" enctype="multipart/form-data">
<input type="file" name="resume" required><br><br>
<button>Analyze Resume</button>
</form>

{% if error %}
<p class="error">{{ error }}</p>
{% endif %}

{% if skills %}

<h3>🧠 Skills</h3>
{% for s in skills %}
<span class="skill">{{ s }}</span>
{% endfor %}

<h3>💼 Job</h3>
<div class="card">{{ job }}</div>

<h3>⭐ Score</h3>
<div class="progress-bar">
<div class="progress" style="width:{{score}}%;background:blue">{{score}}%</div>
</div>

<h3>🎯 Match</h3>
<div class="progress-bar">
<div class="progress" style="width:{{match}}%;background:green">{{match}}%</div>
</div>

<h3>⚠ Missing Skills</h3>
{% for m in missing %}
<span class="skill" style="background:red">{{ m }}</span>
{% endfor %}

{% endif %}

</div>

</body>
</html>

//http://127.0.0.1:5000


