#audio_classrr
Audio one2one call WebRTC kurento based,

#project setup
```
$npm install
$npm start
```


#Application Server Logic

Script applikasi : "server.js"
 - Komunikasi antara Javascript Client dan Aplikasi server Node menggunakan websocket.
 - parameter websocket
	server  : HOST_SERVER
	path 	: one2one
 - Untuk menangani proses komunikasi dan signaling ada beberapa message yang digunakan :
	1. register
	2. call 
	3. incomingCallResponse
	4. onIceCandidate
	5. stop
 - Untuk melakukan panggilan, setiap user (Caller dan Callee) harus melakukan registrasi ke dalam sistem dengan menjalankan fungsi register.
 - Modul incomingCallResponse akan dijalankan ketika "caller" melakukan panggilan dengan mengakses fungsi "call" 
 - Kurento Media Server (KMS) akan menangani pertukaran Interactive Connectivity Establishment (ICE) candidate antara peer WebRTC. 
   Pada proses ini, webRtcEndpoint menerima candidate dari client melalui fungsi OnIceCandidate. 
   candidate-candidate ini disimpan dalam urutan ketika webRtcEndpoint belum tersedia. 
   selanjutnya candidate-candidate ini akan di tambahkan ke elemen media dengan menjalan fungsi addIceCandidate.

#Client-Side Logic
 - Untuk melakukan sambungan websocket ke aplikasi server, dilakukan menggunakan class WebSocket pada javascript.Library yang digunakan adalah kurento-utils.js
```
	ex. var ws = new WebSocket('ws://' + location.host + '/one2one');
```
 - selanjutnya onMessage Listener digunakan untuk menangani  JSON signaling protocol pada sisi client.
 
 - ada tiga incoming message yang digunakan yaitu: startResponse, error, dan iceCandidate.  
 
 ```
 ws.onmessage = function(message) {
   var parsedMessage = JSON.parse(message.data);
   console.info('Received message: ' + message.data);

   switch (parsedMessage.id) {
   case 'registerResponse':
      resgisterResponse(parsedMessage);
      break;
   case 'callResponse':
      callResponse(parsedMessage);
      break;
   case 'incomingCall':
      incomingCall(parsedMessage);
      break;
   case 'startCommunication':
      startCommunication(parsedMessage);
      break;
   case 'stopCommunication':
      console.info("Communication ended by remote peer");
      stop(true);
      break;
   case 'iceCandidate':
      webRtcPeer.addIceCandidate(parsedMessage.candidate)
      break;
   default:
      console.error('Unrecognized message', parsedMessage);
   }
}
```
