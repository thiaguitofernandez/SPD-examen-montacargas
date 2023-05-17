# SPD-examen-montacargas
~~~~
// C++ code
//
#define boton_parar 16
#define boton_subir 15
#define boton_bajar 14
#define	luz_moviendo 13
#define	luz_parado 12
#define	ABAJO_DERECHA 5
#define	ABAJO 6
#define	ABAJO_IZQUIERDA 7
#define	CENTRO 8
#define	ARRIBA_IZQUIERDA 9
#define	ARRIBA 10
#define	ARRIBA_DERECHA 11
int BANDERA_EMERGENCIA = LOW;
int BANDERA_EMERGENCIA_MOVIENDO = LOW;
int piso = 0;
long contador = 0;
void setup()
{
  pinMode(boton_parar, INPUT);
  pinMode(boton_subir, INPUT);
  pinMode(boton_bajar, INPUT);
  pinMode(luz_moviendo, OUTPUT);
  pinMode(luz_parado, OUTPUT);
  pinMode(ABAJO_DERECHA, OUTPUT);
  pinMode(ABAJO, OUTPUT);
  pinMode(ABAJO_IZQUIERDA, OUTPUT);
  pinMode(CENTRO, OUTPUT);
  pinMode(ARRIBA_IZQUIERDA, OUTPUT);
  pinMode(ARRIBA, OUTPUT);
  pinMode(ARRIBA_DERECHA, OUTPUT);
  prende_numero_cero();
  prende_led(luz_parado);
  Serial.begin(9600);
  Serial.println("Usted se encuentra en el piso: 0");
}

void loop()
{
    BANDERA_EMERGENCIA = emergencia(BANDERA_EMERGENCIA);

    if (BANDERA_EMERGENCIA == LOW){
        montacargas();
    }
}
/*funciones principales*/


//funciones para el paro de emegencia
int emergencia(int bandera){
    if( (digitalRead(boton_parar) == HIGH) && (bandera == HIGH)){
        bandera = LOW;
      	contador = contador + millis();
      	Serial.println("Parada de emergencia desactivada");
      	delay(150);
    }
  	else if (digitalRead(boton_parar) == HIGH){
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
      BANDERA_EMERGENCIA_MOVIENDO = emergencia(BANDERA_EMERGENCIA_MOVIENDO);
      while (BANDERA_EMERGENCIA_MOVIENDO == HIGH){
        BANDERA_EMERGENCIA_MOVIENDO = emergencia(BANDERA_EMERGENCIA_MOVIENDO);
        delay(100);
      }
      
      
      
  	}
}
//funcion para mover el montacargas
void montacargas(){
  if(digitalRead(boton_bajar) == HIGH|| digitalRead(boton_subir) == HIGH){
      	control_movimiento();
    	Serial.print("Usted se encuentra en el piso: ");
    	Serial.println(piso);
     	instrucciones_segun_numero();
  }
}          
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
    if ( (digitalRead(boton_subir) == HIGH) || (digitalRead(boton_bajar) == HIGH)){
        apaga_led(luz_parado);
        prende_led(luz_moviendo);
        subir_piso();
        bajar_piso();
		movimiento();
        apaga_led(luz_moviendo);
        prende_led(luz_parado);
    }
}


//entrada de datos
void subir_piso(){
    if ( (digitalRead(boton_subir) == HIGH) && (piso != 9)){
      	Serial.println("Subiendo..");
        piso++;
    }
}
void bajar_piso(){
    if ( (digitalRead(boton_bajar) == HIGH) && (piso != 0)){
      	Serial.println("Bajando..");
        piso--;
    }
}



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
