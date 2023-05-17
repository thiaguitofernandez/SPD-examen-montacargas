# SPD-Examen-Montacargas

## Examen: Montacargas
![Tinkercad](./imagenes/arduino.png)
![Diagrama](./imagenes/arduino_plano_esquematico.jpg)

## objetivo del proyecto
El objetivo de este proyecto es realizar el "menu" interactivo de un montaargas el cual debe subir pisos y bajarlos uno a la vez.
## Funcionamiento
para su funcionamiento se divide en diferentes partes las cuales son las siguientes:
* Configuracion y definicion de variables
* Loop y funciones pincipales
* Entrada de datos
* Funciones para apagado y prendido de leds

### Configuracion inicial y definicion de variables
Aqui se establecen los puertos utilizados asignadoles una denominacion, se inicializan las variables y se establece la configuacion inicial indicando la modalidad de cada puerto como el estado de dichos puertos
* Establecimiento de puertos
~~~~
#define BOTON_PARAR 16
#define BOTON_SUBIR 15
#define BOTON_BAJAR 14
#define	LUZ_MOVIENDO 13
#define	LUZ_PARADO 12
#define	ABAJO_DERECHA 5
#define	ABAJO 6
#define	ABAJO_IZQUIERDA 7
#define	CENTRO 8
#define	ARRIBA_IZQUIERDA 9
#define	ARRIBA 10
#define	ARRIBA_DERECHA 11
~~~~
* Inicializacion de variables
~~~~
int bandera_emergencia = LOW;
int bandera_emergencia_moviendo = LOW;
int piso = 0;
long contador = 0;
~~~~
* Configuracion Inicial
~~~~
void setup()
{
  pinMode(BOTON_PARAR, INPUT);
  pinMode(BOTON_SUBIR, INPUT);
  pinMode(BOTON_BAJAR, INPUT);
  pinMode(LUZ_MOVIENDO, OUTPUT);
  pinMode(LUZ_PARADO, OUTPUT);
  pinMode(ABAJO_DERECHA, OUTPUT);
  pinMode(ABAJO, OUTPUT);
  pinMode(ABAJO_IZQUIERDA, OUTPUT);
  pinMode(CENTRO, OUTPUT);
  pinMode(ARRIBA_IZQUIERDA, OUTPUT);
  pinMode(ARRIBA, OUTPUT);
  pinMode(ARRIBA_DERECHA, OUTPUT);
  prende_numero_cero();
  prende_led(LUZ_PARADO);
  Serial.begin(9600);
  Serial.println("Usted se encuentra en el piso: 0");
}
~~~~

### Loop y funciones pincipales
Dentro del loop se encuenta el codigo que se ejecuta continuamente y luego las funciones pincipales las cuales son las mas importantes.

* Loop
~~~~
void loop()
{
    bandera_emergencia = emergencia(bandera_emergencia);

    if (bandera_emergencia == LOW){
        montacargas();
    }
}
~~~~
El loop consiste de la asignacion de la varaible bandera_emergencia atravez de la funcion emergencia la varaible consigue un valo de HIGH o LOW.
Luego un if evalua esa misma varaible para ver si esta en LOW para poder acceder a la funcion montacargas.
#### funciones principales
Dentro de las funciones pincipales se encuentran 2 funciones que llevan parte del parado de emergencia del montacargas, luego se encuentra la funcion montacargas la cual se encarga de gestionar las funciones necesarias para que este suba y baje reportando el piso en el que esta.

* Funciones para el paro de emegencia
Las funciones para el paro de emerrgencia son:
* emergencia
* movimiento

⋅⋅⋅emergencia es una funcion la cual recibe por parametro una bandera y la devuelve con un valor de HIGH o LOW.
Para esto lee el puerto asociado a BOTON_PARAR para determinar si se esta recibiendo una señal.Luego en conjunto con el valor que la bandera pueda tener determina si debe asignarle HIGH o LOW, siempre para cambiar el valor del mismo y no asignarle un valor que ya tiene.
Otra funcion que emergencia posee es la de invalitar la el resto de botones ya que mientras la bandera que este retorne sea HIGH no se ejecutara mas codigo que esta funcion.

⋅⋅⋅movimiento principalmente se encarga de determinar el tiempo durante el cual el ascensor se encuentra en movimiento.Para esto utiliza la funcion millis(la cual se encuentra explicada en la parte inferior de este documento) y un contador inicializado como long para poder guardar el valor que millis devuelve con un adicional de 3000 milisegundos (el tiempo que el montacargas se mueve de piso en piso).
Lo que permite a la funcion determinar cuanto timepo pasa es un while el cual compara a contador (quien tiene el valor del tiempo a la hora de apretar el boton de inicio del movimiento mas 3 segundos) a la funcion millis y cuando millis devuelva un valor mayor o igual al de contador este terminara la funcion.
Ahora dentro de este while se encuentra una referencia a la funcion emergencia la cual si el BOTON_PARAR es apretado guardaria el tiempo restante del contador para que este llegue a 3 segundos y si el mismo boton es presionado otra vez el valor que millis deberia alcanzar para que el montacargas llegue a su destino sera guardado en contador
~~~~
int emergencia(int bandera){
    if( (digitalRead(BOTON_PARAR) == HIGH) && (bandera == HIGH)){
        bandera = LOW;
      	contador = contador + millis();
      	Serial.println("Parada de emergencia desactivada");
      	delay(150);
    }
  	else if (digitalRead(BOTON_PARAR) == HIGH){
        bandera = HIGH ;
      	contador = contador - millis();
      	Serial.println("Parada de emergencia activada, por favor contacte con mantenimiento");
    	delay(150);
    }
  	
  	return bandera;
}

void movimiento(){
   contador = (millis() + 3000);
   while(contador >= millis()){
     bandera_emergencia_moviendo = emergencia(bandera_emergencia_moviendo);
     while (bandera_emergencia_moviendo == HIGH){
        bandera_emergencia_moviendo = emergencia(bandera_emergencia_moviendo);
     	delay(100);
      }
   }
~~~~ 
* Funcion para mover el montacargas
~~~~
void montacargas(){
  if(digitalRead(BOTON_BAJAR) == HIGH|| digitalRead(BOTON_SUBIR) == HIGH){
      	control_movimiento();
    	Serial.print("Usted se encuentra en el piso: ");
    	Serial.println(piso);
     	instrucciones_segun_numero();
  }
}
~~~~
### Funciones logicas y procesamiento de datos

~~~~          
//funciones logicas para funcionamiento
void instrucciones_segun_numero(){
    switch (piso)
    {
      case (0):
        apaga_numero_uno();
        prende_numero_cero();
        break;
      case (1):
        apaga_numero_cero();
        apaga_numero_dos();
        prende_numero_uno();
        break;
      case (2):
        apaga_numero_uno();
        apaga_numero_tres();
        prende_numero_dos();
        break;
      case (3):
        apaga_numero_dos();
        apaga_numero_cuatro();
        prende_numero_tres();
        break;    
      case (4):
        apaga_numero_tres();
        apaga_numero_cinco();
        prende_numero_cuatro();
        break;
      case (5):
        apaga_numero_cuatro();
        apaga_numero_seis();
        prende_numero_cinco();
        break;
      case (6):
        apaga_numero_cinco();
        apaga_numero_siete();
        prende_numero_seis();
        break;
      case (7):
        apaga_numero_seis();
        apaga_numero_ocho();
        prende_numero_siete();
        break;
      case (8):
        apaga_numero_siete();
        apaga_numero_nueve();
        prende_numero_ocho();
        break;
      case (9):
        apaga_numero_ocho();
        prende_numero_nueve();
        break;
    }
}

void control_movimiento(){
    if ( (digitalRead(BOTON_SUBIR) == HIGH) || (digitalRead(BOTON_BAJAR) == HIGH)){
        apaga_led(LUZ_PARADO);
        prende_led(LUZ_MOVIENDO);
        subir_piso();
        bajar_piso();
		movimiento();
        apaga_led(LUZ_MOVIENDO);
        prende_led(LUZ_PARADO);
    }
}
~~~~ 

### Entrada de datos
~~~~ 
//entrada de datos
void subir_piso(){
    if ( (digitalRead(BOTON_SUBIR) == HIGH) && (piso != 9)){
      	Serial.println("Subiendo..");
        piso++;
    }
}
void bajar_piso(){
    if ( (digitalRead(BOTON_BAJAR) == HIGH) && (piso != 0)){
      	Serial.println("Bajando..");
        piso--;
    }
}
~~~~ 
### Funciones para apagado y prendido de leds
~~~~ 

//funciones para apagar y prender led-->>
void apaga_led(int led){
  digitalWrite(led, LOW);
}
void prende_led(int led){  	
	digitalWrite(led, HIGH);
}
void prende_numero_cero(){
  prende_led(ARRIBA);
  prende_led(ARRIBA_DERECHA);
  prende_led(ARRIBA_IZQUIERDA);
  prende_led(ABAJO_DERECHA);
  prende_led(ABAJO_IZQUIERDA);
  prende_led(ABAJO);
}
void apaga_numero_cero(){
  apaga_led(ARRIBA);
  apaga_led(ARRIBA_DERECHA);
  apaga_led(ARRIBA_IZQUIERDA);
  apaga_led(ABAJO_DERECHA);
  apaga_led(ABAJO_IZQUIERDA);
  apaga_led(ABAJO);
}
  
void prende_numero_uno(){
  prende_led(ARRIBA_DERECHA);
  prende_led(ABAJO_DERECHA);
}
  void apaga_numero_uno(){
  apaga_led(ARRIBA_DERECHA);
  apaga_led(ABAJO_DERECHA);
}

void prende_numero_dos(){
  prende_led(ARRIBA);
  prende_led(ARRIBA_DERECHA);
  prende_led(CENTRO);
  prende_led(ABAJO_IZQUIERDA);
  prende_led(ABAJO);
}
void apaga_numero_dos(){
  apaga_led(ARRIBA);
  apaga_led(ARRIBA_DERECHA);
  apaga_led(CENTRO);
  apaga_led(ABAJO_IZQUIERDA);
  apaga_led(ABAJO); 
}

void prende_numero_tres(){
  prende_led(ARRIBA);
  prende_led(ARRIBA_DERECHA);
  prende_led(CENTRO);
  prende_led(ABAJO_DERECHA);
  prende_led(ABAJO);
}
void apaga_numero_tres(){
  apaga_led(ARRIBA);
  apaga_led(ARRIBA_DERECHA);
  apaga_led(CENTRO);
  apaga_led(ABAJO_DERECHA);
  apaga_led(ABAJO);  
}

void prende_numero_cuatro(){
  prende_led(ARRIBA_IZQUIERDA);
  prende_led(ARRIBA_DERECHA);
  prende_led(CENTRO);
  prende_led(ABAJO_DERECHA);
}
void apaga_numero_cuatro(){
  apaga_led(ARRIBA_IZQUIERDA);
  apaga_led(ARRIBA_DERECHA);
  apaga_led(CENTRO);
  apaga_led(ABAJO_DERECHA);  
}

void prende_numero_cinco(){
  prende_led(ARRIBA);
  prende_led(ARRIBA_IZQUIERDA);
  prende_led(CENTRO);
  prende_led(ABAJO_DERECHA);
  prende_led(ABAJO); 
}
void apaga_numero_cinco(){
  apaga_led(ARRIBA);
  apaga_led(ARRIBA_IZQUIERDA);
  apaga_led(CENTRO);
  apaga_led(ABAJO_DERECHA);
  apaga_led(ABAJO);   
}

void prende_numero_seis(){
  prende_led(ARRIBA);
  prende_led(ARRIBA_IZQUIERDA);
  prende_led(CENTRO);
  prende_led(ABAJO_DERECHA);
  prende_led(ABAJO_IZQUIERDA);
  prende_led(ABAJO);
}
void apaga_numero_seis(){
  apaga_led(ARRIBA);
  apaga_led(ARRIBA_IZQUIERDA);
  apaga_led(CENTRO);
  apaga_led(ABAJO_DERECHA);
  apaga_led(ABAJO_IZQUIERDA);
  apaga_led(ABAJO);
}

void prende_numero_siete(){
  prende_led(ARRIBA);
  prende_led(ARRIBA_DERECHA);
  prende_led(ABAJO_DERECHA);
}
void apaga_numero_siete(){
  apaga_led(ARRIBA);
  apaga_led(ARRIBA_DERECHA);
  apaga_led(ABAJO_DERECHA);
}

void prende_numero_ocho(){
  prende_led(ARRIBA);
  prende_led(ARRIBA_DERECHA);
  prende_led(ARRIBA_IZQUIERDA);
  prende_led(CENTRO);
  prende_led(ABAJO_DERECHA);
  prende_led(ABAJO_IZQUIERDA);
  prende_led(ABAJO);
}
void apaga_numero_ocho(){
  apaga_led(ARRIBA);
  apaga_led(ARRIBA_DERECHA);
  apaga_led(ARRIBA_IZQUIERDA);
  apaga_led(CENTRO);
  apaga_led(ABAJO_DERECHA);
  apaga_led(ABAJO_IZQUIERDA);
  apaga_led(ABAJO);
}

void prende_numero_nueve(){
  prende_led(ARRIBA);
  prende_led(ARRIBA_DERECHA);
  prende_led(ARRIBA_IZQUIERDA);
  prende_led(CENTRO);
  prende_led(ABAJO_DERECHA);
}
void apaga_numero_nueve(){
  apaga_led(ARRIBA);
  apaga_led(ARRIBA_DERECHA);
  apaga_led(ARRIBA_IZQUIERDA);
  apaga_led(CENTRO);
  apaga_led(ABAJO_DERECHA);
}
~~~~
