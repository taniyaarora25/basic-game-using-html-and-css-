# basic-game-using-html-and-css-
<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<style>
canvas {
    border:1px solid #d3d3d3;
    background-color: #41DF16;
}
</style>
</head>
<body onload="startGame()">
<script>

var myGamePiece;
var myObstacles = [];
var myScore;
var mySound;
var myMusic;
var myBackground;

function startGame() {
    myGamePiece = new component(50, 50, "image.png", 10, 120,"image");
    //myGamePiece.gravity = 0.05;
    myScore = new component("30px", "Consolas", "black", 280, 40, "text");
    myBackground = new component(656, 270, "image1.png", 0, 0, "image");
     mySound = new sound("bounce.mp3");
     myMusic = new sound("king.mp3");
     

    myGameArea.start();
    
    myMusic.play();
     }

var myGameArea = {
    canvas : document.createElement("canvas"),
    start : function() {

        this.canvas.width = 720;
        this.canvas.height = 480;
        this.context = this.canvas.getContext("2d");
        document.body.insertBefore(this.canvas, document.body.childNodes[0]);
        this.frameNo = 0;
        this.interval = setInterval(updateGameArea, 20);
        },
    clear : function() {
        this.context.clearRect(0, 0, this.canvas.width, this.canvas.height);
    },
    stop : function() {
        clearInterval(this.interval);
    }
}

function component(width, height, color, x, y, type) {
    this.type = type;
     if (type == "image" || type == "background") {
        this.image = new Image();
        this.image.src = color;
    }
    this.score = 0;
    this.width = width;
    this.height = height;
    this.speedX = 0;
    this.speedY = 0;    
    this.x = x;
    this.y = y;
    this.gravity = 0;
   //his.gravitySpeed = 0;
    this.update = function() {
        ctx = myGameArea.context;
        
       
        if (type == "image" || type == "background") {
            ctx.drawImage(this.image, 
                this.x, 
                this.y,
                this.width, this.height);
        if (type == "background") {
            ctx.drawImage(this.image, 
                this.x + this.width, 
                this.y,
                this.width, this.height);
        }}
        else {
            ctx.fillStyle = color;
            ctx.fillRect(this.x, this.y, this.width, this.height);
        }
    }
   
    this.newPos = function() {
        //is.gravitySpeed += this.gravity;
        this.x += this.speedX;
        this.y += this.speedY ;
        if (this.type == "background") {
            if (this.x == -(this.width)) {
                this.x = 0;
            }
        }
        this.hitBottom();
        myMusic.play();
    }
    this.hitBottom = function() {
        var rockbottom = myGameArea.canvas.height - this.height;
        if (this.y > rockbottom) {
            this.y = rockbottom;
            this.gravitySpeed = 0;
        }
    }
    this.crashWith = function(otherobj) {
        var myleft = this.x;
        var myright = this.x + (this.width);
        var mytop = this.y;
        var mybottom = this.y + (this.height);
        var otherleft = otherobj.x;
        var otherright = otherobj.x + (otherobj.width);
        var othertop = otherobj.y;
        var otherbottom = otherobj.y + (otherobj.height);
        var crash = true;
        if ((mybottom < othertop) || (mytop > otherbottom) || (myright < otherleft) || (myleft > otherright)) {
            crash = false;
        }
        return crash;
    }
}

function updateGameArea() {
    var x, height, gap, minHeight, maxHeight, minGap, maxGap;
    for (i = 0; i < myObstacles.length; i += 1) {
        if (myGamePiece.crashWith(myObstacles[i])) {
             mySound.play();
             myMusic.stop();
            myBackground.speedX = -1;
    myBackground.newPos();    
    myBackground.update();
    myGamePiece.newPos();    
    myGamePiece.update();
            
            return;
        } 
    }
    myGameArea.clear();
    myGameArea.frameNo += 1;
    if (myGameArea.frameNo == 1 || everyinterval(150)) {
        x = myGameArea.canvas.width;
        y=myGameArea.canvas.height;
        minHeight = 20;
        maxHeight = 200;
        minWidth=40;
        maxWidth=200;
        height = Math.floor(Math.random()*(maxHeight-minHeight+1)+minHeight);
        width=Math.floor(Math.random()*(maxWidth-minWidth+1)+minWidth);
        minGap = 70;
        maxGap = 200;
        gap = Math.floor(Math.random()*(maxGap-minGap+1)+minGap);
        myObstacles.push(new component(10, height, "red", x, 0));
        myObstacles.push(new component(10, x - height - gap, "red", x, height + gap));
        myObstacles.push(new component(0,y,"red",10,width));
        myObstacles.push(new component(0,y,"red",10,y-width-gap));
    }
    for (i = 0; i < myObstacles.length; i += 1) {
        myObstacles[i].x += -1;
        myObstacles[i].update();
    }
    myScore.text="SCORE: " + myGameArea.frameNo;
    myScore.update();
    myGamePiece.newPos();
    myGamePiece.update();
}
function sound(src) {
    this.sound = document.createElement("audio");
    this.sound.src = src;
    this.sound.setAttribute("preload", "auto");
    this.sound.setAttribute("controls", "none");
    this.sound.style.display = "none";
    document.body.appendChild(this.sound);
    this.play = function(){
        this.sound.play();
    }
    this.stop = function(){
        this.sound.pause();
    }    
}

function everyinterval(n) {
    if ((myGameArea.frameNo / n) % 1 == 0) {return true;}
    return false;
}
function move(dir) {
    myGamePiece.image.src = "angry.gif";
    if (dir == "up") {myGamePiece.speedY = -1; }
    if (dir == "down") {myGamePiece.speedY = 1; }
    if (dir == "left") {myGamePiece.speedX = -1; }
    if (dir == "right") {myGamePiece.speedX = 1; }
}
function clearmove() {
    myGamePiece.image.src = "smiley.gif";
    myGamePiece.speedX = 0; 
    myGamePiece.speedY = 0; 
}

</script>
<br>
<!--utton onmousedown="accelerate(-0.2)" onmouseup="accelerate(0.05)">ACCELERATE</button> -->
<div style="text-align:center;width:480px;">
  <button onmousedown="move('up')" onmouseup="clearmove()" ontouchstart="move('up')">UP</button><br><br>
  <button onmousedown="move('left')" onmouseup="clearmove()" ontouchstart="move('left')">LEFT</button>
  <button onmousedown="move('right')" onmouseup="clearmove()" ontouchstart="move('right')">RIGHT</button><br><br>
  <button onmousedown="move('down')" onmouseup="clearmove()" ontouchstart="move('down')">DOWN</button>
</div>

<!--<p>Use the ACCELERATE button to stay in the air</p>-->

</body>
</html>
