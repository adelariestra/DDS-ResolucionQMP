# ResolucionQMP4
## Código/Pseudocódigo
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
  
  public validoParaTemperatura(int temperatura)
    return temperatura<tempMaximaAdecuada;
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
	
  // De una categoria obtener un elemento de la lista prendas
  
  // De una lista de categorias obtener un elemento de prendas por cada categoria en base al clima
}

```
## Diagrama de Clases
[Pending]
