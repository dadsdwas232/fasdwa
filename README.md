from flask import Flask, request, jsonify
import random, string, sqlite3, datetime, os

app = Flask(__name__)
DB = "keys.db"

# ================= DATABASE =================
def init_db():
    conn = sqlite3.connect(DB)
    c = conn.cursor()
    c.execute("""
        CREATE TABLE IF NOT EXISTS keys (
            key TEXT PRIMARY KEY,
            used INTEGER,
            created TEXT,
            expires TEXT,
            userid TEXT
        )
    """)
    conn.commit()
    conn.close()

init_db()

# ================= KEY =================
def generate_key():
    return "RXT-" + ''.join(
        random.choices(string.ascii_uppercase + string.digits, k=22)
    )

# ================= HTML =================
HTML_PAGE = """<!DOCTYPE html>
<html lang="ar">
<head>
<meta charset="UTF-8">
<title>RXT Key System</title>
<style>
body{margin:0;height:100vh;display:flex;justify-content:center;align-items:center;
background:linear-gradient(135deg,#0f0c29,#302b63,#24243e);font-family:Tahoma;color:white;}
.box{width:380px;padding:35px;text-align:center;border-radius:18px;
background:rgba(0,0,0,.45);box-shadow:0 0 30px rgba(140,70,255,.6);}
h1{margin:0 0 8px;color:#b57aff;}
.domain{font-size:13px;opacity:.85;margin-bottom:25px;}
button{background:#7b2cff;border:none;color:white;padding:14px 28px;
border-radius:12px;font-size:16px;cursor:pointer;transition:.3s;}
button:hover{background:#9b5cff;box-shadow:0 0 18px #9b5cff;}
.keybox{display:none;margin-top:20px;background:rgba(255,255,255,.12);
padding:12px;border-radius:10px;word-break:break-all;}
.note{font-size:12px;opacity:.7;margin-top:15px;}
</style>
</head>
<body>
<div class="box">
<h1>RXT Key System</h1>
<div class="domain">RXT.Kyes.rxt.com</div>
<button onclick="createKey()">🔑 إنشاء مفتاح</button>
<div id="keybox" class="keybox"></div>
<div class="note">المفتاح صالح 24 ساعة – استخدام واحد</div>
</div>
<script>
function createKey(){
fetch("/generate",{method:"POST"})
.then(r=>r.json())
.then(d=>{
let box=document.getElementById("keybox");
box.style.display="block";
box.innerText=d.key;
});
}
</script>
</body>
</html>"""

# ================= ROUTES =================
@app.route("/")
def home():
    return HTML_PAGE

@app.route("/generate", methods=["POST"])
def generate():
    key = generate_key()
    now = datetime.datetime.utcnow()
    expires = now + datetime.timedelta(hours=24)

    conn = sqlite3.connect(DB)
    c = conn.cursor()
    c.execute(
        "INSERT INTO keys VALUES (?,?,?,?,?)",
        (key, 0, now.isoformat(), expires.isoformat(), None)
    )
    conn.commit()
    conn.close()

    return jsonify({"key": key})

@app.route("/verify", methods=["POST"])
def verify():
    data = request.json
    key = data.get("key")
    userid = data.get("userid")

    conn = sqlite3.connect(DB)
    c = conn.cursor()
    c.execute(
        "SELECT used, expires, userid FROM keys WHERE key=?",
        (key,)
    )
    row = c.fetchone()

    if not row:
        return jsonify({"status":"invalid"})

    used, expires, saved_user = row

    if used:
        return jsonify({"status":"used"})

    if datetime.datetime.fromisoformat(expires) < datetime.datetime.utcnow():
        return jsonify({"status":"expired"})

    if saved_user and saved_user != userid:
        return jsonify({"status":"invalid_user"})

    c.execute(
        "UPDATE keys SET used=1, userid=? WHERE key=?",
        (userid, key)
    )
    conn.commit()
    conn.close()

    return jsonify({"status":"valid"})

# ================= RUN (IMPORTANT) =================
if __name__ == "__main__":
    port = int(os.environ.get("PORT", 5000))
    app.run(host="0.0.0.0", port=port)
