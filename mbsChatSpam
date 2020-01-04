let mbsChatSpam = function () {
  this.run = true;
  this.username = 'zzzzzzzz';
  this.password = 'oauth:xxxxxxxxxxxx';
  this.channel = '#promic2';
  this.server = 'irc-ws.chat.twitch.tv';
  this.port = 443;

  this.ws = new WebSocket('wss://' + this.server + ':' + this.port + '/', 'irc');
  this.ws.onmessage = this._message.bind(this);
  this.ws.onerror = this._error.bind(this);
  this.ws.onclose = this._close.bind(this);
  this.ws.onopen = this._open.bind(this);

  this.uTypes = ['!flash','!disco','!police','!hex ff0038','!off'];
  let i = 0;
  this.loop = setInterval(() => {
    if (this.ws.readyState === 1 && this.run === true) {
      let slc = Math.floor( Math.random() * this.uTypes.length );
      let msg = this.uTypes[slc];

      this.uTypes = ['!flash','!disco','!police','!hex ff0038','!off'];
      let index = this.uTypes.indexOf(msg);
      if (index > -1) {
         this.uTypes.splice(index, 1);
      }

      this.ws.send(`PRIVMSG #promic2 :${msg}`);
    } else {
      console.log('Skipped by run=off');
    }
  }, 10000);
}

mbsChatSpam.prototype._error = function (cb) {
  console.log('Error: ' + cb);
}

mbsChatSpam.prototype._close = function (cb) {
  console.log('Disconnected from the chat server.');
  clearInterval(this.loop);
}

mbsChatSpam.prototype._open = function (cb) {
  console.log(cb);
  let ws = this.ws;

  if (ws !== null && ws.readyState === 1) {
      console.log('Connecting and authenticating...');

      ws.send('CAP REQ :twitch.tv/tags twitch.tv/commands twitch.tv/membership');
      ws.send('PASS ' + this.password);
      ws.send('NICK ' + this.username);
      ws.send('JOIN ' + this.channel);
  }
}

mbsChatSpam.prototype._message = function (cb) {
  if(cb !== null){
    var parsed = this.parseMessage(cb.data);
    if(parsed !== null){
      if(parsed.command === "PRIVMSG") {
        //console.log(parsed);
      } else if(parsed.command === "PING") {
        this.webSocket.send("PONG :" + parsed.message);
      }
    }
  }
}

mbsChatSpam.prototype.parseMessage = function parseMessage(rawMessage) {
    var parsedMessage = {
        message: null,
        tags: null,
        command: null,
        original: rawMessage,
        channel: null,
        username: null
    };

    if(rawMessage[0] === '@'){
        var tagIndex = rawMessage.indexOf(' '),
        userIndex = rawMessage.indexOf(' ', tagIndex + 1),
        commandIndex = rawMessage.indexOf(' ', userIndex + 1),
        channelIndex = rawMessage.indexOf(' ', commandIndex + 1),
        messageIndex = rawMessage.indexOf(':', channelIndex + 1);

        parsedMessage.tags = rawMessage.slice(0, tagIndex);
        parsedMessage.username = rawMessage.slice(tagIndex + 2, rawMessage.indexOf('!'));
        parsedMessage.command = rawMessage.slice(userIndex + 1, commandIndex);
        parsedMessage.channel = rawMessage.slice(commandIndex + 1, channelIndex);
        parsedMessage.message = rawMessage.slice(messageIndex + 1);
    } else if(rawMessage.startsWith("PING")) {
        parsedMessage.command = "PING";
        parsedMessage.message = rawMessage.split(":")[1];
    }

    return parsedMessage;
}
