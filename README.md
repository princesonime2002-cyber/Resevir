# Resevir
My Resevir
<!DOCTYPE html>
<html lang="hi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Mobile TV Remote</title>

<script src="https://unpkg.com/peerjs@1.5.4/dist/peerjs.min.js"></script>

<style>
body {
    background: #101827;
    color: white;
    font-family: Arial;
    text-align: center;
    padding: 20px;
}

.box {
    background: #1e293b;
    padding: 20px;
    border-radius: 15px;
    margin: 15px auto;
    max-width: 500px;
}

input, textarea {
    width: 90%;
    padding: 14px;
    margin: 10px;
    border-radius: 8px;
    border: none;
    font-size: 18px;
}

button {
    padding: 14px 20px;
    margin: 5px;
    border: none;
    border-radius: 8px;
    background: #0d9488;
    color: white;
    font-size: 17px;
}

#status {
    color: #4ade80;
}
</style>
</head>

<body>

<h1>📺 Mobile TV Remote</h1>
<p id="status">Connecting...</p>

<div class="box">
<h2>TV MODE</h2>
<p>आपका TV Connection Code:</p>
<h1 id="tvCode">Loading...</h1>

<textarea id="tvScreen"
rows="6"
placeholder="यहाँ मोबाइल से लिखा हुआ टेक्स्ट दिखाई देगा">
</textarea>
</div>

<div class="box">
<h2>MOBILE MODE</h2>

<input id="connectCode"
placeholder="TV का Code डालें">

<button onclick="connectTV()">Connect TV</button>

<textarea id="mobileText"
rows="5"
placeholder="यहाँ टाइप करें..."></textarea>

<button onclick="sendText()">SEND TO TV</button>

<br>

<button onclick="sendKey('ENTER')">ENTER</button>
<button onclick="sendKey('BACKSPACE')">⌫</button>
<button onclick="sendKey('SPACE')">SPACE</button>
<button onclick="sendKey('LEFT')">←</button>
<button onclick="sendKey('RIGHT')">→</button>

</div>

<script>

let peer;
let connection;
let mode = "tv";

function setStatus(text) {
    document.getElementById("status").innerText = text;
}

function startTV() {

    peer = new Peer();

    peer.on("open", function(id) {
        document.getElementById("tvCode").innerText = id;
        setStatus("TV Ready");
    });

    peer.on("connection", function(conn) {

        connection = conn;

        conn.on("open", function() {
            setStatus("Mobile Connected!");
        });

        conn.on("data", function(data) {

            if(data.type === "text") {
                document.getElementById("tvScreen").value = data.value;
            }

            if(data.type === "key") {
                handleKey(data.value);
            }

        });

    });
}

function connectTV() {

    const code = document.getElementById("connectCode").value;

    connection = peer.connect(code);

    connection.on("open", function() {
        setStatus("Connected to TV!");
    });

    connection.on("error", function() {
        setStatus("Connection Error");
    });
}

function sendText() {

    if(connection && connection.open) {

        const value = document.getElementById("mobileText").value;

        connection.send({
            type: "text",
            value: value
        });

    } else {
        alert("पहले TV Connect करें");
    }
}

function sendKey(key) {

    if(connection && connection.open) {
        connection.send({
            type: "key",
            value: key
        });
    }
}

function handleKey(key) {

    const screen = document.getElementById("tvScreen");

    if(key === "ENTER") {
        screen.value += "\n";
    }

    if(key === "SPACE") {
        screen.value += " ";
    }

    if(key === "BACKSPACE") {
        screen.value = screen.value.slice(0,-1);
    }

}

startTV();

</script>

</body>
</html>
