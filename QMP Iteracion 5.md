---
# QMP Iteración 5
---
## Requerimientos
GRAL: Como usuarie de QuéMePongo, quiero compartir mis guardarropas con otras personas.

1. Como usuarie de QuéMePongo, quiero poder manejar varios guardarropas para separar mis prendas según diversos criterios (ropa de viaje, ropa de entrecasa, etc). 
Sol: usuarie con lista de guardarropas.

2. Como usuarie de QuéMePongo, quiero poder crear guardarropas compartidos con otros usuaries (ej, ropa que comparto con mi hermane).
Sol: usuarie puede agregar guardarropas previamente instanciados.

3. Como usuarie de QuéMePongo, quiero que otro usuario me proponga tentativamente agregar una prenda al guardarropas.
Sol: ver implementacion.

4. Como usuarie de QuéMePongo, quiero que otro usuario me proponga tentativamente quitar una prenda del guardarropas.
Sol: ver implementacion.

5. Como usuarie de QuéMePongo, necesito ver todas las propuestas de modificación (agregar o quitar prendas) del guardarropas y poder aceptarlas o rechazarlas..
Sol: Usuarie contiene una lista de propuestas. 

6. Como usuarie de QuéMePongo, quiero poder deshacer las propuestas de modificación que haya aceptado.
Sol: ver implementacion.

### Notas
Inicialmente el guardarropas tenia sus propuestas e historial pero si el guardarropas esta dentro de la propuesta y el historial forma parte del usuario separo mejor responsabilidades.
Además, las propuestas son para un usuario y múltiples usuarios pueden tener instanciado un guardarropas, por lo que el guardarropas no puede tener las propuestas.

Code Smell: Guardarropas actúa como DataClass. Podría modelarlo como una lista de prendas y que los usuarios tengan varios lavarropas en forma de lista de listas de prendas y que varios usuarios compartan listas de prendas apuntando a la misma instancia en sus listas. Pierdo expresividad por lo que decido mantener la DataClass por el momento.

Que el guardarropas entienda los mensajes agregar(prenda) y elimiar(prenda) o que la propuesta lo haga con guardarropas.contenido.add/remove(prenda) ?
- Opcion 1 es mas compleja (2 metodos menos que en un principio podrían parecer pasamanos) pero tengo menos acoplamiento entre propuesta y el guardarropas. Aprovecho la elección de abstraer el guardarropas por más que tenga un DataClass y comienza a tener métodos.
- Opcion 2 **quizas** es más simple pero definitivamente más acoplada, estaría sabiendo hasta como implementa guardarropas su contenido.



## Código/Pseudocódigo
```java

// USUARIO
public class Usuarie{
	ArrayList<Propuesta> propuestas;
	ArrayList<Propuesta> historialAceptado;
	ArrayList<Guardarropas> listaDeGuardarropas; 
	
	public Usuarie(){
		propuestas = new ArrayList<Propuesta>();
		historialAceptado = new ArrayList<Propuesta>();
		listaDeGuardarropas = new ArrayList<Guardarropas>();

	}

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
}

// SERVICIOS METEREOLOGICOS
public interface ServicioMetereologico{
	public List<Map<String, Object>> obtenerInformacionClima(string locacion);
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
	public ArrayList<Prenda> obtenerRecomendaciones(ArrayList<Categoria> categorias){
		return categorias.stream().map(categoria ->this.obtenerRecomendacion(categoria));;
	}
}

```

## Diagrama de Clases
[Pending]
