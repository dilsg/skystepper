#include <Arduino.h>
#include <WiFi.h>
#include <AsyncTCP.h>
#include <ESPAsyncWebServer.h>
#include <Stepper.h>
// Stepper Motor Settings
const int stepsPerRevolution = 2048;  // change this to fit the number of steps per revolution
#define IN1 19
#define IN2 18
#define IN3 5
#define IN4 17
Stepper myStepper(stepsPerRevolution, IN1, IN3, IN2, IN4);
// Replace with your network credentials
const char* ssid = "SSIDxxxx";
const char* password = "SSID_PWxxxx";
// Create AsyncWebServer object on port 80
AsyncWebServer server(80);
// Search for parameters in HTTP POST request
const char* PARAM_INPUT_1 = "direction";
const char* PARAM_INPUT_2 = "steps";
// Variables to save values from HTML form
String direction;
String steps;
// Variable to detect whether a new request occurred
bool newRequest = false;
// HTML to build the web page
const char index_html[] PROGMEM = R"rawliteral(
<!DOCTYPE html>
<html>
<head>
  <title>Stepper Motor 28BYJ-48 mit ULN2003
</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<style>
table {
  border-collapse: collapse;
  width: 100%;
}

th, td {
  padding: 1px;
  text-align: left;
  border-bottom: 1px solid #DDD;
}

td:hover {background-color: #D6EEEE;}


.button {
  background-color: #04AA6D; /* Green */
  border: none;
  color: white;
  padding: 5px 5px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  cursor: pointer;
}

.button2 {background-color: #008CBA;} /* Blue */
.button3 {background-color: #f44336;} /* Red */ 
.button4 {background-color: #e7e7e7; color: black;} /* Gray */ 
.button5 {background-color: #555555;} /* Black */

</style>
<body>
<script>
function myplus100() {
  document.getElementById('steps').value = 100;
  document.getElementById('direction').value = "CW";
}
function myplus1000() {
  document.getElementById('steps').value = 1000;
  document.getElementById('direction').value = "CW";
}
function myplus10() {
  document.getElementById('steps').value = 10;
  document.getElementById('direction').value = "CW";
}
function myminus100() {
  document.getElementById('steps').value = 100;
  document.getElementById('direction').value = "CCW";
}
function myminus1000() {
  document.getElementById('steps').value = 1000;
  document.getElementById('direction').value = "CCW";
}
function myminus10() {
  document.getElementById('steps').value = 10;
  document.getElementById('direction').value = "CCW";
}
</script>
  <h1>Stepper Motor Control</h1>
    <form action="/" method="POST">
      <input type="radio" id="direction" name="direction" value="CW" checked>
      <label for="CW">Clockwise</label>
      <input type="radio" id="direction" name="direction" value="CCW">
      <label for="CW">Counterclockwise</label><br><br><br>
      <label for="steps">Number of steps:</label>
      <input type="number" id="steps" name="steps">
      <input type="submit" value="GO!">
      Position: 
      <table>
      <tbody><tr>
      <td bgcolor="red"} > 
        <button class="button button3" id="Plus100" onclick="myminus1000()">-1000<p></p> 
      </button></td><td bgcolor="red">
        <button class="button button3" id="Plus100" onclick="myminus100()">-100<p></p>
      </button></td><td bgcolor="red">
        <button class="button button3" id="Plus100" onclick="myminus10()">-10<p></p> 
      </button></td><td>&nbsp; &nbsp; 
      </td><td bgcolor="blue">
        <button class="button button2" id="Plus100" onclick="myplus10()">+10<p></p> 
      </button></td><td bgcolor="blue">
        <button class="button button2" id="Plus100" onclick="myplus100()">+100<p></p> 
      </button></td><td bgcolor="blue">
        <button class="button button2" id="Plus100" onclick="myplus1000()">+1000<p></p>
      </button></td></tr>
    </tbody></table>
      
    </form>
    


</body>
</html>
)rawliteral";
// Initialize WiFi
void initWiFi() {
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  Serial.print("Connecting to WiFi ..");
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print('.');
    delay(1000);
  }
  Serial.println(WiFi.localIP());
}

void setup() {
  Serial.begin(115200);
  initWiFi();
  myStepper.setSpeed(5);
  // Web Server Root URL
  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send(200, "text/html", index_html);
  });
  
  // Handle request (form)
  server.on("/", HTTP_POST, [](AsyncWebServerRequest *request) {
    int params = request->params();
    for(int i=0;i<params;i++){
      const AsyncWebParameter* p = request->getParam(i);
      if(p->isPost()){
        // HTTP POST input1 value (direction)
        if (p->name() == PARAM_INPUT_1) {
          direction = p->value().c_str();
          Serial.print("Direction set to: ");
          Serial.println(direction);
        }
        // HTTP POST input2 value (steps)
        if (p->name() == PARAM_INPUT_2) {
          steps = p->value().c_str();
          Serial.print("Number of steps set to: ");
          Serial.println(steps);
        }
      }
    }
    request->send(200, "text/html", index_html);
    newRequest = true;
  });
  server.begin();
}
void loop() {
  // Check if there was a new request and move the stepper accordingly
  if (newRequest){
    if (direction == "CW"){
      // Spin the stepper clockwise direction
      myStepper.step(steps.toInt());
    }
    else{
      // Spin the stepper counterclockwise direction
      myStepper.step(-steps.toInt());
    }
    newRequest = false;
  }
}
