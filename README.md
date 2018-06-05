






Projeto Objetos Inteligentes Conectados 

Ajustador de distancia ultrassônico








Fernando Ventura 
Murilo Motta
Pedro Egydio
Rafael Nascimento 











Turma: 5J11



	Nosso projeto tem como alicerce um sensor ultrassônico HC-SR04 para podermos utilizar da sua resposta em distância e assim darmos coordenadas ao usuário final, tanto quanto para se aproximar de seu objetivo, como se distanciar. Tendo em vista que a aula de objetos inteligentes do quinto semestre da faculdade de Sistemas da Informação, tinha o objetivo de nos dar como inspiração o conceito de Iot para esse projeto, utilizamos de uma página http para se comunicar via Wi-fi com nossa WEMOS D1 R1 (uma placa Arduino que já possuía um modulo Wi-fi instalado).
	De forma pratica e rápida, nossa pagina tem uma interface de fácil compreensão onde haverá um box de entrada de dados para se digitar a distancia preterida e a saída logo após indica o quanto falta em cm para a distancia desejada, assim como se é necessário se distanciar ou aproximar. 
	Colocamos também uma margem de erro de 2cm, que é a mesma do nosso sensor ultrassônico HC-SR04, ou seja caso a distância desejada esteja em 2cm pra mais ou menos a saída final sera como você alcançou seu objetivo.


Objetivos:
	Ajudar o usuário final da interface http a conseguir se localizar e pontuar seus objetivos utilizando a métrica de cm.

Diagrama de Casos de Uso:

 
Interface do usuário 

 


Plataformas de Desenvolvimento: Fritzing e Tinkercad

Desenho Tinkercad

 

Circuito eletrico
 




Materiais utilizados no projeto:

Sensores:
1 Sensor ultrassônico HC-SR04

Arduino:
1 WEMOS D1 R1

Outros:
1 Protoboard
4 Jumpers
Imagens Equipamentos

1.	WEMOS D1 R1 

 

2.	Sensor ultrassônico HC-SR04

 







3.	Protoboard 
 

4.	Jumpers 

 





















Código do projeto:



#include <ESP8266WiFi.h>
 
const char* ssid = "DistribuidoraMalta";
const char* password = "malta2013";
 
WiFiServer server(80);

#include <Ultrasonic.h>
 
#define pino_trigger D4
#define pino_echo D5
Ultrasonic ultrasonic(pino_trigger, pino_echo);
 
void setup() {
  
  
  Serial.begin(115200);
  delay(10);
 
  // Connect to WiFi network
  Serial.println();
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);
 
  WiFi.begin(ssid, password);
 
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("WiFi connected");
 
  // Start the server
  server.begin();
  Serial.println("Server started");
 
  // Print the IP address
  Serial.print("Use this URL : ");
  Serial.print("http://");
  Serial.print(WiFi.localIP());
  Serial.println("/");
 
}
 
void loop() {
  
  // Check if a client has connected
  WiFiClient client = server.available();
  if (!client) {
    return;
  }
 
  // Wait until the client sends some data
  Serial.println("new client");
  while(!client.available()){
    delay(1);
  }
 
  // Read the first line of the request
  String request = client.readStringUntil('\r');
  Serial.println(request);
  client.flush();
 
 
  // Return the response
  client.println("HTTP/1.1 200 OK");
  client.println("Content-Type: text/html");
  client.println(""); //  do not forget this one
  client.println("<!DOCTYPE HTML>");
  client.println("<html>");
  
  float distancia; 
  client.print("<h1><center>Bem vindo a trena eletronica!  </h1></center>");
  client.println("<br><br>");
  client.println("<form action='' method='GET'>");
  client.println("Distancia desejada: <input type='text' value='$distancia'cm>");
  client.println("<br><br>");
  client.println("<input type='submit' value='Enviar'>");
  client.println("<br><br>");
  client.println("</form>");
  client.println("</html>");
  client.println();
  float diferenca;
  float cmMsec;
  
  long microsec = ultrasonic.timing();
  cmMsec = ultrasonic.convert(microsec, Ultrasonic::CM);
  client.print("<b>Distancia que voce esta do objeto:</b> ");
  client.print(cmMsec);
  client.print(" cm");
  client.println("<br><br>");
  diferenca = distancia - cmMsec ;
  client.print("<b>A distancia desejada pelo usuario: </b>");
  client.print(distancia);
  client.print(" cm");
  client.println("<br><br>");
  client.println("<br><br>");
  client.print("<b>A diferenca entre a distancia informada pelo usuario e a distancia real: </b>");
  client.print(diferenca);
  client.print(" cm");
  client.println("<br><br>");
  client.println("<br><br>");

  if (diferenca+1 > 0){
    client.print("<b><h2>Aproxime-se</h1></b>");
  }
  else if (diferenca+1 < 0){
    client.print("<b><h2>Distancie-se</h2></b>");
  }
  else {
    client.print("<b><h2>A distancia esta correta</h2></b>");
  }

  
 
  delay(1);
  Serial.println("Client disconnected");
  Serial.println("");
 
}



Link do repositorio github: https://github.com/EgydioLucas/Objetos-inteligentes-

