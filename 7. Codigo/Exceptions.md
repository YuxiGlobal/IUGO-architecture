# Manejo de excepciones

Para el manejo de excepciones se propone la siguiente guía de Microsoft:
https://docs.microsoft.com/en-us/dotnet/standard/exceptions/best-practices-for-exceptions

A continuación se definen algunos guidelines lo cuales serán aplicados en cada uno de los proyectos:

## Prevenir excepciones

Las excepciones deben ser manejadas de manera preventiva, por ejemplo, si se requiere una conversión a número de un string es preferible utilizar `Int32.TryParse`.

## Propagación de excepciones

A no ser de que se desee hacer una validación de negocio o una traducción de una excepción presentada, se recomienda dejar que las excepciones se progaguen hasta la capa de protocolo, donde se tendrá un filtro para centralizar el manejo de las excepciones.

## Evitar relanzamiento de excepciones

Las excepciones no deben relanzarse en escenarios normales, para evitar la pérdida de stacktrace e información necesaria de la excepción, a no ser de que se vaya a hacer un tratamiendo específico de la excepción como Log, tranducción a Excepción de negocio u otro, las excepciones no se deben relanzar:

Mal ejemplo:
```c#

try 
{
	this.repository.FindAll();
}
catch(Exception ex)
{
	throw ex;
}

```

Escenario de uso:
```c#

try 
{
	 result = File.ReadAllLines(fileName);
}
catch(Exception ex)
{
	throw ConversionFileAccessException(filename, ex);
}

```


Siempre que se relance una excepción se debe agregar el parámetro en el constructor `InnerException` lo que brindará más información al Manejador de excepciones que se implemente en la aplicación.

## Web Api

En el caso de las Web Api se han definido algunas estratégias para manejar las excepciones propias para esta tecnología, lo cual hará ue sea más efectivo el manejo de escenarios comunes:

### Model Validation Filter

Se recomienda crear un `ActionFilterAttribute` general para la validación de los modelos en relación con los `DataAnnotations` que hayamos creado para validar la integridad de los datos de entrada, esto para evitar poner en cada `Action` de los Controllers líneas recurrentes como `ModelState.IsValid`. A continuación un ejemplo de implementación:

```c#
	public class ValidateModelAttribute: ActionFilterAttribute
	{
		public override void OnActionExecuting(ActionExecutingContext filterContext)
		{
			if (!filterContext.ModelState.IsValid)
			{
				filterContext.Result = new BadRequestObjectResult(filterContext.ModelState);
			}
		}
	}

```

### Manejo de excepciones Web Api

Se recomienda la creación de un `FilterAttribute` para el manejo de exceptiones, se debe tener muy en cuenta que todo manejo de excepciones debe cumplir:

1. Guardar Log de la excepción con información valiosa que pueda llevarnos a una solución pronta, por ejemplo, información de stacktrace o información de la petición que falló **Tener en cuenta que no se deben guardar en Log datos sensibles**.
1. La respuesta de la Web Api en un ambiente productivo para una excepción, por seguridad, nunca debe devolver un mensaje que de información de la implementación, como el stacktrace o el mensaje directo del error, por lo tanto el Filter Attribute de Excepciones es el mejor candidato para traducir todas las Excepciones a un mensaje personalizado a mostrar en la respuesta de la Web Api. Tener en cuenta que si se tiene una excepción personalizada con un mensaje que se quiera mostrar como respuesta, este filtro también podría cambiarse para mostrar un distinto mensaje para un tipo de excepción.
1. Recordar utilizar los códigos Http correspondientes a cada error presentado en la aplicación.

Ejemplo de Filtro con manejo de excepción:

```c#
 public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }

```
Para más información: https://docs.microsoft.com/en-us/aspnet/web-api/overview/error-handling/exception-handling

## Excepciones Personalizadas

1. **Excepciones Personalizadas sobre Códigos de Error:** Se prefiere tener Excepciones personalizadas sobre devolver códigos de error en una respuesta para ser validada por el consumidor.
1. **Constructores Excepciones personalizadas:** Todas las excepciones personalizadas que se creen deben implementar por lo menos los constructores básicos de la clase `Exception` (message) y (message, innerException).
1. **Propiedades para transmitir información:** En caso de que se requieran más datos hacerca de un mensaje de error por parte de los consumidores, se deben utilizar propiedades en la excepción personalizada para transmitirlos, por ejemplo, si una excepción que se cree tiene como objetivo comunicar una excepción en validación de importación de un archivo plano, se podría crear una propiedad que indique  el número de la línea donde ocurrió el error.