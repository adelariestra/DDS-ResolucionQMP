---
# QMP Iteración 6
---
## Requerimientos
GRAL: Como usuarie de QueMePongo quiero poder enterarme si se emitió alguna alerta meteorológica para poder actuar en consecuencia.

1. Usuarie > **recibir sugerencia diaria prenda** **actualizada** todas las mañannas.

2. Empleade> **disparar cálculo de sugerencias diarias** para **todos los usuaries** 
nota: para despues automatizar y disparalo todas las mañanas

3. Usuarie> **conocer** cuáles son las **últimas alertas meteorológicas publicadas en el sistema**.
nota: por eg al ingresar a la pagina

4. Empleade> **disparar consulta y actualizacion** de la **lista de alertas publicadas en el sistema** para tener control sobre cuándo se publican las mismas nota: control de que??? 

5. Usuarie> que **se actualice mi sugerencia diaria** con las condiciones climáticas actualizadas **cuando se genere algún alerta** durante el día. 

6. Usuarie> **ante una alerta de tormenta** la app **me notifique que debo llevarme también un paraguas**. 

7. Usuarie> **ante una alerta de granizo** la app  **me notifique que evite salir en auto**.

8. Usuarie> **recibir un mail avisándome si se generó algún alerta meteorológico y cuál**.

9. Usuarie> **configurar cuáles de estas acciones** quiero que se ejecuten y cuáles no.
nota: notificaciones, mail, recálculo

**Soportar nuevas acciones a futuro**. (No nos interesará, sin embargo, soportar nuevas alertas)

### Notas
Se cuenta con una API Para Java del sistema operativo que envía notificaciones en la pantalla al usuario mientras navega el sitio:

NotificationService>>notify(text)

Se cuenta con un servicio externo de mails:

MailSender>>send(address,message)


Está super rancia la abstracción del clima, se usa a veces, a veces no y a veces es un pasamanos. Revisar.
Código en diferentes idiomas.
A veces metodos en mayuscula y otros en minuscula.

3. El usuario puede consultar las alertas conociendo el EmisorNotificaciones.
2. Puede mandar un mensaje a EmisorNotificaciones de EnviarRecomendacione()

## Código/Pseudocódigo
```java

// USUARIE EMPLEADE
public class EmisorNotificaciones{
	ArrayList<Usuarie> usuaries;
	List<String> alerts;
	Clima servicioClima;

	public EmisorRecomendaciones(Clima servicioClima){
		this.servicioClima = servicioClima;	//requirenonull...
	}

	public void EnviarRecomendaciones(){
		for(Usuarie user : usuaries)
			user.NuevaNotificacion(new RecalculoRecomendacion("Actualizacion",user));
	}

	public void ActualizarAlertas(){
		alerts = servicioClima.GetCurrentAlerts();
		this.EnviarRecomendaciones();
		this.EnviarNotificaciones();
	}

	
	public void EnviarNotificaciones(){
		for(Usuarie user : usuaries)
			for(String alert : alerts){
				switch(alert) {
				  case "Hail":
				    user.NuevaNotificacion(new AlertaMetereologicaPopup("No salgas en auto!",user));
				    break;
				  case "Storm":
				    user.NuevaNotificacion(new AlertaMetereologicaPopup("Lleva paraguas!",user));
				    break;
				  default:
				    //comportamiento default, nada
				}
				user.NuevaNotificacion(new AlertaMetereologicaMail(alert,user));
			}
	}



}

public abstract class Notificacion{
	String alerta;
	Usuarie usuarie;

	public Notificacion(String alerta, Usuarie usuarie){
		this.alerta = alerta;
		this.usuarie = usuarie;
	}

	void notificar();
}

public class RecalculoRecomendacion : Notificacion{
	void notificar(){
		if(usuarie.recalculoActivado)
			usuarie.recomedacionDiaria = usuarie.emisorRecomendaciones.obtenerRecomendaciones(Categoria.values());
	}
}

public class AlertaMetereologicaMail : Notificacion{
	void notificar(){
		if(usuarie.mailsActivados)
			MailSender mailSender = new MailSender();
			mailSender.send(usuarie.address, alerta); 

	}
}

public class AlertaMetereologicaPopup : Notificacion{
	void notificar(){
		if(usuarie.notificacionesActivadas)
			notifService.notify(alert);	//Como lo conoce? Singleton? DI?
	}
}

// USUARIO
public class Usuarie{
	ArrayList<Propuesta> propuestas;
	ArrayList<Propuesta> historialAceptado;
	ArrayList<Guardarropas> listaDeGuardarropas;

 	ArrayList<Prenda> recomedacionDiaria;
	EmisorRecomendaciones emisorRecomendaciones;	
	
	Boolean mailsActivados;
	Boolean recalculoActivado;
	Boolean notificacionesActivadas;

	String address;

	public Usuarie(ServicioMetereologico servicio, String address){
		propuestas = new ArrayList<Propuesta>();
		historialAceptado = new ArrayList<Propuesta>();
		listaDeGuardarropas = new ArrayList<Guardarropas>();
		emisorRecomendaciones = new EmisorRecomendaciones(servicio,listaDeGuardarropas.get(0).contenido);
		this.address = address;
		// no esta definido a partir de que obtener la recomendacion
	}

	// Recomendaciones
	public void NuevaNotificacion(Notificacion notificacion){
		notificacion.notificar();
	}

	// Guardarropas

	public void AgregarGuardarropas(Guardarropas guardarropas){
		listaDeGuardarropas.add(requireNonNull(guardarropas,"Guardarropas no válido"))
	}

	public void AgregarPropuestaPrenda(Propuesta propuesta){ //recibo la prop directo para que no se acople Usuario a Propuesta sabiendo que contiene prenda etc.
		propuestas.add(propuesta);
	}

	public void AceptarPropuesta(Propuesta propuesta){
		historialAceptado.add(propuesta);
		propuestas.remove(propuesta);
		propuesta.aceptar();
	}

	public void DeshacerPropuesta(Propuesta propuesta){
		historialAceptado.remove(propuesta);
		propuesta.deshacer();
		// Hace falta agregarla a la lista de propuestas de nuevo? preguntar negocio
	}
}

// GUARDARROPAS
public class Guardarropas{
	ArrayList<Prenda> contenido;

	public Guardarropas(ArrayList<Prenda> contenido){
		this.contenido = contenido; //Puedo tener un guardarropas sin contenido? Por ahora si.
	}

	public void agregar(Prenda prenda){
		contenido.add(prenda)
	}

	public void eliminar(Prenda prenda){
		contenido.remove(prenda)
	}
}

// PROPUESTA
public abstract class Propuesta{ //no interfaz porque tiene el constructor
	Prenda prenda;
	Guardarropas guardarropas;

	public Propuesta(Prenda prenda, Guardarropas guardarropas){
		this.prenda = requireNonNull(prenda, "Una propuesta debe tener una prenda.");
		this.guardarropas = requireNonNull(guardarropas, "Una propuesta debe tener un guardarropas");
	}

	public abstract aceptar();
	public abstract deshacer()
}

public class PropuestaEliminar implements Propuesta{
	aceptar(){
		guardarropas.eliminar(prenda);
	}
	deshacer(){
		guardarropas.agregar(prenda);
	}
}

public class PropuestaEliminar implements Propuesta{
	deshacer(){
		guardarropas.eliminar(prenda);
	}
	aceptar(){
		guardarropas.agregar(prenda);
	}
}

// PRENDA
public class Prenda {
	TipoDePrenda tipoDePrenda;
	Material material;
	Color color;
	Color colorSecundario; // Si no lo defino, no pasa nada
	Trama trama;
	int tempMaximaAdecuada;

	// Estas validaciones requireNonNull estan en Borrador. Sacar?
	public Prenda(TipoDePrenda tipo, Material material, Color color, Trama trama, int tempMaximaAdecuada){
		this.tipoDePrenda = requireNonNull(tipo, "tipo de prenda es obligatorio");
		this.trama = requireNonNull(trama, "trama de prenda es obligatorio");
		this.material = requireNonNull(material, "material es obligatorio");
		this.color = requireNonNull(color, "color es obligatorio");
		this.tempMaximaAdecuada = requireNonNull(tempMaximaAdecuada, "temperatura maxima adecuada para la prenda es obligatorio");
	}

	public especificarColorSecundario(Color color){
		this.colorSecundario = requireNonNull(color, "color es obligatorio");
	}
  
  	public bool validoParaTemperatura(int temperatura){
    		return temperatura<tempMaximaAdecuada;
  	}
}

// BORRADOR
public class Borrador : Prenda{
	public Borrador(TipoDePrenda tipo)
		this.tipoDePrenda = requireNonNull(tipo, "tipo de prenda es obligatorio");

	public void especificarTrama(Trama trama)
		this.trama = if trama is null then Trama.LISA else trama

	public void especificarMaterial(Material material)
		this.validarMaterialConsistenteConTipoDePrenda(material);
		this.material = requireNonNull(material, "material es obligatorio");

	public void especificarColorPrincipal(Color color)
		this.color = requireNonNull(color, "color es obligatorio");

	public void especificarTempMaximaAdecuada(int temperatura)
		this.tempMaximaAdecuada = requireNonNull(temperatura, "temperatura maxima adecuada para la prenda es obligatorio");


	public Prenda crearPrenda()
		return new Prenda(tipo, material, colorPrincipal, colorSecundario, trama);

}

// TRAMA
public enum Trama{
	LISA, RAYADA, ...;
}

// TIPO PRENDA
public class TipoDePrenda{
	Categoria categoria;

	public TipoDePrenda(Categoria categoria)
		this.categoria = categoria;
	
	public Categoria categoria()
		return this.categoria;
}

// CATEGORIA
public enum Categoria{
	PARTE_SUP,CALZADO,PAARTE_INF,ACCESORIO
}

// COLOR
public class Color{
	int rojo, verde, azul
	public Color (int rojo, int verde, int azul){
		this.rojo = rojo;
		this.verde = verde;
		this.azul = azul;
	}
}

// SASTRE Y UNIFORMES - TODO: Pasar a javaish
class Uniforme{
	attribute prendaSuperior
	attribute prendaInferior
	attribute calzado

	classmethod fabricar(Sastre sastre)
	new Uniforme(
		sastre.fabricarParteSuperior(),
		sastre.fabricarParteInferior(),
		sastre.fabricarCalzado())
}

interface Sastre{
	method fabricarParteSuperior()
	method fabricarParteInferior()
	method fabricarCalzado()
}

class SastreSanJuan implements Sastre{
	method fabricarParteSuperior()
		borrador = new Borrador(CHOMBA)
		borrador.especificarColor(new Color(....))
		borrador.especificarMaterial(PIQUE)
		return borrador.crearPrenda()
	method fabricarParteInferior()
		borrador = new Borrador(PANTALON)
		borrador.especificarColor(new Color(....))
		borrador.especificarMaterial(ACETATO)
		return borrador.crearPrenda()
		//etc...
}

// API ACCUWEATHER - Porcion de código copiada
public final class AccuWeatherAPI{
    public final List<Map<String, Object>> getWeather(String ciudad) 
		return Arrays.asList(new HashMap<String, Object>(){{
			put("DateTime", "2019-05-03T01:00:00-03:00");
			put("EpochDateTime", 1556856000);
			put("WeatherIcon", 33);
			put("IconPhrase", "Clear");
			put("IsDaylight", false);
			put("PrecipitationProbability", 0);
			put("MobileLink", "http://m.accuweather.com/en/ar/villa-vil/7984/");
			put("Link", "http://www.accuweather.com/en/ar/villa-vil/7984");
			put("Temperature", new HashMap<String, Object>(){{
				put("Value", 57);
				put("Unit", "F");
				put("UnitType", 18);
			}});
		}});
	public final Map<String,List<String>> getAlerts(String ciudad){
		//Returns alertas
	}
}

// SERVICIOS METEREOLOGICOS
public interface ServicioMetereologico{
	public List<Map<String, Object>> obtenerInformacionClima(String locacion);

	public List<String> obtenerAlertas(String locacion);
}

// SERVICIO ACCUWEATHER
public class RespuestaMeteorologica {
	List<Map<String, Object>> infoMetereologica;
	DateTime expiracion;

  	public RespuestaMeteorologica(List<Map<String, Object>> infoMetereologica, DateTime expiracion) {
		this.infoMetereologica = infoMetereologica;	//+ chequeo de nulls
		this.expiracion = expiracion; //+ chequeo de nulls
	}

	public getInfoMetereologica(){
		return List<Map<String, Object>> infoMetereologica;
	} 

	public expiro(){
		return expiracion < LocalDateTime.now();
	}
}

public class ServicioAccuweather implements ServicioMetereologico{
	AccuWeatherAPI servicioAccuweather = new AccuWeatherAPI();
	Map<String, RespuestaMeteorologica> respuestasPrevias;
	DateTime dataDuration;

	ServicioAccuweather(DateTime dataDuration){
		this.dataDuration = dataDuration;
	}

	ServicioAccuweather(){
		this.dataDuration = new DateTime(0,0,0,0,15,0);
	}

	public List<String> getCurrentAlerts(String locacion){
		return servicioAccuweather.getAlertas(locacion).get("CurrentAlerts");
	}

	List<Map<String, Object>> obtenerInformacionClima(string locacion) {
		if (!this.respuestasPrevias.contains(locacion) || 
			this.respuestasPrevias.get(locacion).expiro()) {

			respuestasPrevias.put(new RespuestaMeteorologica(servicioAccuweather.getWeather(locacion),nuevaExpiracion())); //agregar key de locacion	
		}
		return this.respuestasPrevias.get(locacion).getInfoMetereologica();
	}

	public DateTime nuevaExpiracion(){
		return LocalDateTime.now() + dataDuration;
	}
}

// CLIMA ACTUAL
public class Clima {
	ServicioMetereologico servicioMetereologico;
	public Clima(ServicioMetereologico servicio){
		this.ServicioMetereologico = requireNonNull(servicio,"Debe especificarse un servicio");
	}
	
	public int obtenerTemperaturaActualBuenosAires(){
		return servicioMetereologico.obtenerInformacionClima("Buenos Aires,Argentina")get(0).get("Temperature");
	}
	
	public List<Map<String, Object>> obtenerInformacionClima(string locacion) {
		return servicioMetereologico.obtenerInformacionClima(locacion);	
	}

	public List<String> getCurrentAlerts(){
		return servicioMetereologico.getCurrentAlerts("Buenos Aires,Argentina"); //Pasamanos parametrizado
	}
}

// RECOMENDACIONES
public class EmisorRecomendaciones {
	public ArrayList<Prenda> prendas;
	Clima servicioClima;

	public EmisorRecomendaciones(ServicioMetereologico servicio, ArrayList<Prenda> prendas){
		requireNonNull(servicio,"Debe especificarse un sercicio para obtener el clima");
		servicioClima = new Clima(servicio);
		this.prendas = prendas;
	}
	
  	// De una categoria obtener un elemento de la lista prendas
  	public Prenda obtenerRecomendacion(Categoria categoria){ 
		return prendas.filter(prenda -> 
				prenda.categoria == categoria && 
				prenda.validoParaTemperatura(servicioClima.obtenerTemperaturaActualBuenosAires()))
			.findFirst();
	}
	
	// De una lista de categorias obtener un elemento de prendas por cada categoria en base al clima
	public ArrayList<Prenda> (ArrayList<Categoria> categorias){
		return categorias.stream().map(categoria ->this.obtenerRecomendacion(categoria));;
	}
}

```

## Diagrama de Clases
[Pending]