var wsUri = "ws://72.187.52.12:10965";
var InGameName = getCookie("InGameName");
var FriendCode = getCookie("FriendCode");
var RoomName = "";
var InGameName2 = "";
var FriendCode2 = "";
var Clients = "0";
var Battles = "0";
var Connected = false;
var InRoom = false;
var RoomName = '';

function init() {
    document.getElementById('header').innerHTML = 'Connecting to server...';
    testWebSocket();
}
 
function testWebSocket() {
    websocket = new WebSocket(wsUri);
    websocket.onopen = function (evt) { onOpen(evt) };
    websocket.onclose = function (evt) { onClose(evt) };
    websocket.onmessage = function (evt) { onMessage(evt) };
    websocket.onerror = function (evt) { onError(evt) };
}

function onOpen(evt) {
    Connected = true;
    SendPacket("0");
    SetLogin();
}

function onClose(evt) {
    if (Connected) {
        SetLostConnection();
    }
    else {
        SetOffline();
    }
}

function UpdateStats() {
    var users = 'users';
    var battles = 'battles';

    if (Clients == 1) {
        users = 'user';
    }

    if (Battles == 1) {
        battles = 'battle';
    }

    document.getElementById('stats').innerHTML = Clients + ' ' + users + ' online and ' + Battles + ' active ' + battles;
}

function SendPacket(msg) {
    websocket.send(msg);
}

function onMessage(evt) {
    var msg = evt.data.split('\n');
    switch (msg[0]) {
        case "0"://Stats
            {
                Clients = msg[1];
                Battles = msg[2];
                UpdateStats();
            }
            break;

        case "1"://Update Rooms
            {
                if (msg[1] == '') {
                    document.getElementById('body').innerHTML = '';
                }
                else if (msg[4] == '') {
                    document.getElementById('body').innerHTML += '<div id="content"><h1>' + msg[2] + '</h1><fieldset><ul><li><label for="host">' + msg[3] + '</label></li><li><label for="guest">' + msg[4] + '</label></li><li><input type="button" value="Join" class="large blue button" onclick="JoinRoom(' + msg[1] + ');" /></li></ul></fieldset></div>';
                }
                else {
                    document.getElementById('body').innerHTML += '<div id="content"><h1>' + msg[2] + '</h1><fieldset><ul><li><label for="host">' + msg[3] + '</label></li><li><label for="guest">' + msg[4] + '</label></li></ul></fieldset></div>';
                }
            }
            break;

        case "2"://Join Rooms
            {
                InGameName2 = msg[1];
                FriendCode2 = msg[2];
                if(!InRoom)
                {
                    SetRoom(msg[3]);
                }
                document.getElementById('info').innerHTML = 'In Game Name: ' + InGameName2 + '<br>Friend Code: ' + FriendCode2;
                writeToScreen(InGameName2 + ' has joined.<br>');
            }
            break;

        case "3"://Opponent left room
            {
                writeToScreen(InGameName2 + " has left.<br>");
                InGameName2 = '';
                FriendCode2 = '';
                document.getElementById('info').innerHTML = 'In Game Name: ' + InGameName2 + '<br>Friend Code: ' + FriendCode2;
            }
            break;

        case "9"://Chat
            {
                writeToScreen(msg[1]);
            }
            break;

    }
}

function onError(evt) {
    if (Connected) {
        SetLostConnection();
    }
    else {
        SetOffline();
    }
}

function SetLostConnection() {
    document.getElementById('header').innerHTML = 'The connection was dropped. :(';
    document.getElementById('body').innerHTML = '';
}

function SetOffline() {
    document.getElementById('header').innerHTML = 'The server is offline. :(';
    document.getElementById('body').innerHTML = '';
}

function SetLogin() {
    InRoom = false;
    document.getElementById('header').innerHTML = '<div id="stats"></div><h1>Wi-Fi Battle Finder</h1><fieldset><ul><li><label for="ingamename">In Game Name<input type="text" id="ign" maxlength="12"/></label></li><li><label for="friendcode">Friend Code<input type="text" id="fc" maxlength="14"/></label></li><li><input type="button" value="Login" class="large blue button" onclick="Login();" /></li></ul></fieldset>';
    document.getElementById('body').innerHTML = '';
    document.getElementById('ign').value = InGameName;
    document.getElementById('fc').value = FriendCode;
    UpdateStats();
}

function SetHome() {
    InRoom = false;
    document.getElementById('header').innerHTML = '<div id="stats"></div><h1>Create A Room</h1><fieldset><ul><li><label for="roomname">Room Name<input type="text" id="rn" maxlength="50"/></label></li><li><input type="button" value="Create" class="large blue button" onclick="MakeRoom();" /></li><li><label for="spacer"></label></li></ul></fieldset>';
    document.getElementById('body').innerHTML = '';
    UpdateStats();
}

function SetRoom(RoomName) {
    InRoom = true;
    document.getElementById('header').innerHTML = '<div id="stats"></div><h1>' + RoomName + '</h1><fieldset><ul><li><label for="info"><div id=info><div></label></li><li><label for="chat"><div id="chatbox"></div><input type="text" id="usermsg" size="63" autocomplete="off" onKeydown="Javascript: if ((event.keyCode || event.wich )==13) SendMessage();" maxlength="1024"/></label></li><li><input type="button" value="Leave" class="large blue button" onclick="goHome();"/></li></ul></fieldset>';
    document.getElementById('info').innerHTML = 'In Game Name: ' + InGameName2 + '<br>Friend Code: ' + FriendCode2;
    document.getElementById('chatbox').innerHTML = '';
    UpdateStats();
    writeToScreen(InGameName + ' has joined.<br>');
}

function goHome() {
    SetHome();
    SendPacket("4\n");
}

function Login() {
    InGameName = document.getElementById('ign').value;
    FriendCode = document.getElementById('fc').value;

    if (InGameName.length == 0) {
        alert('What\'s your IGN?');
        return;
    }

    if ((FriendCode.length != 14) && (FriendCode.charAt(4) != '-') && (FriendCode.charAt(9) != '-')) {
        alert('What\'s your FC? (0123-4567-8910)');
        return;
    }

    SendPacket("1\n" + InGameName + "\n" + FriendCode);
    setCookie("InGameName", InGameName, 30);
    setCookie("FriendCode", FriendCode, 30);
    InGameName = sanitizedText(InGameName);
    FriendCode = sanitizedText(FriendCode);
    SetHome();
}

function MakeRoom() {
    RoomName = document.getElementById('rn').value;

    if (InGameName.length == 0) {
        alert('What\'s your room\'s name?');
        return;
    }

    SendPacket("2\n" + RoomName);
    SetRoom(sanitizedText(RoomName));
}

function JoinRoom(room)
{
    InRoom = false;
    SendPacket("3\n" + room);
}

function SendMessage() {
    if (document.getElementById('usermsg').value != "") {
        SendPacket("9\n" + document.getElementById('usermsg').value);

        writeToScreen('<b>' + InGameName + '</b>:&nbsp;' + ConvertUrlsToLinks(sanitizedText(document.getElementById('usermsg').value)) + '<br>');
        document.getElementById("usermsg").value = '';
    }
}

function writeToScreen(message) {
    var div = document.getElementById('chatbox');
    div.innerHTML = div.innerHTML + message;
    div.scrollTop = div.scrollHeight;
}

function ConvertUrlsToLinks(message) {
    var exp = /(\b(https?|ftp|file):\/\/[-A-Z0-9+&@#\/%?=~_|!:,.;]*[-A-Z0-9+&@#\/%=~_|])/ig;
    return message.replace(exp, '<a href="$1" target="_blank">$1</a>');
}

function setCookie(cname, cvalue, exdays) {
    var d = new Date();
    d.setTime(d.getTime() + (exdays * 24 * 60 * 60 * 1000));
    var expires = "expires=" + d.toGMTString();
    document.cookie = cname + "=" + cvalue + "; " + expires;
}

function getCookie(cname) {
    var name = cname + "=";
    var ca = document.cookie.split(';');
    for (var i = 0; i < ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') c = c.substring(1);
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

function sanitizedText(Text) {
    return Text.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;").replace(/"/g, "&quot;");
}

window.addEventListener("load", init, false);
