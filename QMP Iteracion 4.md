---
# QMP Iteración 4
---
## Entrega Corregida

```java
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
Correcciones:
1. No apliqué una resolución para el segundo punto: cache de data para reducir consultas a API paga.
2. Noto a simple vista que la clase Clima actúa casi como pasamanos (sin ver la resolución, nice), pero si el EmisorRecomendaciones interactúa directamente con el servicio va a estar ultra acoplado, no puede recibir una lista de objetos.
No hace falta que implemente el obtenertemperaturaActual(...) en cada servicioMetereologico. Por ahora puedo hacer que la clase clima a partir de la info climatica de cualquier servicio obtenga dif características como la temperatura, humedad, etc de esa data. 
Está bastante acoplado ya que si cambia como entrega la data la api cambio tanto Clima como ServicioAccuweather pero por ahora no se me ocurre mejor opción. Apesta a **adapters**.
3. Terminar implementación de Emisor de Recomendaciones

## Diagrama de Clases
[Pending]

## Mi entrega
```java
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
	public int obtenerTemperaturaActual(string locacion);

	public List<Map<String, Object>> obtenerInformacionClima(string locacion);
}

// SERVICIO ACCUWEATHER
public class ServicioAccuweather implements ServicioMetereologico{
	int obtenerTemperaturaActual(string locacion){
		return obtenerInformacionClima(locacion).get(0).get("Temperature");
	}
	
	List<Map<String, Object>> obtenerInformacionClima(string locacion) {
		AccuWeatherAPI servicioAccuweather = new AccuWeatherAPI();
		return infoClima =  servicioAccuweather.getWeather(locacion);
	}
}

// CLIMA ACTUAL
public class Clima {
	ServicioMetereologico servicioMetereologico;
	public Clima(ServicioMetereologico servicio){
		this.ServicioMetereologico = requireNonNull(servicio,"Debe especificarse un servicio");
	}
	
	public int obtenerTemperaturaActualBuenosAires(){
		return servicioMetereologico.obtenerTemperaturaActual("Buenos Aires,Argentina");
	}
	
	public List<Map<String, Object>> obtenerInformacionClima(string locacion) {
		return servicioMetereologico.obtenerInformacionClima(locacion);	
	}
}

// RECOMENDACIONES
public class EmisorRecomendaciones {
	public ArrayList<Prenda> prendas;
	
  	/* De una categoria obtener un elemento de la lista prendas
  	public Prenda obtenerRecomendacion(Categoria categoria){ 
	// Agregar validacion de clima: "&& prenda.validoParaTemperatura(clima.obtenerTemperaturaActualBuenosAires())"
		//return prendas.where(prenda => prenda.categoria == categoria).first();
	}
	*/
	/* De una lista de categorias obtener un elemento de prendas por cada categoria en base al clima
	public ArrayList<Prenda> obtenerRecomendaciones(ArrayList<Categoria> categorias){
		ArrayList<Prenda> prendasRecomendadas;
		foreach(Categoria categoria:categorias)	//Reemplazar por map
			prendasRecomendadas.Add(this.obtenerRecomendacion(categoria));
		return prendasRecomendadas;
	}
	*/
}

```