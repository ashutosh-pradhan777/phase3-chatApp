var express = require("express")
var app = express()
var mongoose = require("mongoose")
var http = require("http").Server(app)
var io= require("socket.io")(http)
var bodyParser = require('body-parser')

var url = "mongodb://localhost:27017/chatapp"
app.use(express.static(__dirname))
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }))

var Chats = mongoose.model("Chats", {
    name: String,
    chat: String
})

app.post("/chats", async (req, res) => {
    try {
        var chat = new Chats(req.body)
        await chat.save()
        res.sendStatus(200)
        io.emit("chat", req.body)
    } catch (error) {
        res.sendStatus(500)
        console.error(error)
    }
})

app.get("/chats", (req, res) => {
    Chats.find({}, (error, chats) => {
        res.send(chats)
    })
})

mongoose.connect(url,
    (err) => {
    console.log("Connected Successfully...", err)
})


http.listen(3000,()=>{
 console.log("listening on port 3000...")
})