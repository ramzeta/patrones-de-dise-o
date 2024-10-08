# Patrones de Diseño en Spring Boot: Qué Solucionan y Cuándo Usarlos 🎯

Spring Boot es una herramienta poderosa que simplifica el desarrollo de aplicaciones Java al proporcionar configuraciones predeterminadas y una fácil integración con el ecosistema Spring. Una de las razones por las que Spring Boot es tan eficaz es por su uso extensivo de patrones de diseño. Estos patrones ofrecen soluciones probadas a problemas comunes en el desarrollo de software, mejorando la modularidad, reutilización y mantenibilidad del código.

En este artículo, exploraremos algunos de los patrones de diseño más relevantes en Spring Boot, qué problemas resuelven y cuándo es apropiado utilizarlos. ¡Vamos allá! 🚀

## 1. Inyección de Dependencias (Dependency Injection) 🧩

### ¿Qué Problema Resuelve?

La Inyección de Dependencias es un principio fundamental en Spring Boot que promueve la inversión de control. Permite que los objetos no gestionen sus propias dependencias, sino que estas se proporcionen externamente. Esto reduce el acoplamiento entre componentes y facilita las pruebas unitarias y la mantenibilidad.

### ¿Cuándo Usarlo?

Siempre que necesites que un objeto dependa de otro para funcionar, es recomendable usar la inyección de dependencias. En Spring Boot, esto se logra fácilmente mediante anotaciones como `@Autowired`, `@Component`, `@Service` y `@Repository`.

**Ejemplo:**

```java
@Service
public class UsuarioService {
    private final UsuarioRepository usuarioRepository;

    public UsuarioService(UsuarioRepository usuarioRepository) {
        this.usuarioRepository = usuarioRepository;
    }
}
```

## 2. Patrón Singleton ♾️

### ¿Qué Problema Resuelve?

El patrón Singleton asegura que una clase tenga solo una instancia y proporciona un punto de acceso global a ella. En el contexto de Spring Boot, los beans definidos son, por defecto, singletons dentro del contenedor de Spring, lo que significa que la misma instancia se reutiliza en toda la aplicación.

### ¿Cuándo Usarlo?

Cuando necesitas garantizar que solo exista una instancia de una clase en toda la aplicación, como gestores de configuración o conexiones a recursos únicos.

**Ejemplo:**

```java
@Component
public class ConfiguracionGlobal {
    // Esta clase será un singleton en el contexto de Spring
}
```

## 3. Patrón Template Method 📋

### ¿Qué Problema Resuelve?

El patrón Template Method define el esqueleto de un algoritmo en una clase base y permite que las subclases redefinan ciertos pasos sin cambiar la estructura general. En Spring, esto se ve en clases como `JdbcTemplate` y `RestTemplate`, que proporcionan métodos para operaciones comunes y permiten personalizaciones específicas.

### ¿Cuándo Usarlo?

Cuando tienes un algoritmo general con pasos que pueden variar según el contexto, pero quieres mantener una estructura común.

**Ejemplo:**

```java
public abstract class ProcesadorArchivo {
    public void procesar() {
        leerArchivo();
        procesarDatos();
        guardarResultados();
    }

    protected abstract void leerArchivo();
    protected abstract void procesarDatos();
    protected abstract void guardarResultados();
}
```

## 4. Patrón Proxy 🕵️‍♂️

### ¿Qué Problema Resuelve?

El patrón Proxy proporciona un sustituto o lugar para controlar el acceso a otro objeto. En Spring Boot, es fundamental para la implementación de Aspect-Oriented Programming (AOP), permitiendo agregar comportamientos como logging, seguridad o transacciones sin modificar el código original.

### ¿Cuándo Usarlo?

Cuando necesitas agregar funcionalidad adicional antes o después de la ejecución de métodos existentes sin alterar su implementación.

**Ejemplo:**

```java
@Aspect
@Component
public class LoggingAspect {
    @Before("execution(* com.example.servicio.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        // 📝 Lógica de logging antes de la ejecución del método
    }
}
```

## 5. Patrón Observador (Observer) 👀

### ¿Qué Problema Resuelve?

El patrón Observer define una dependencia de uno a muchos entre objetos, de manera que cuando uno cambia de estado, notifica a sus dependientes. Spring Boot utiliza este patrón en su sistema de eventos, permitiendo que los componentes se suscriban y reaccionen a eventos publicados en el contexto de la aplicación.

### ¿Cuándo Usarlo?

Cuando necesitas que diferentes partes de tu aplicación reaccionen a ciertos eventos de manera desacoplada.

**Ejemplo:**

```java
// Evento personalizado
public class UsuarioCreadoEvento extends ApplicationEvent {
    public UsuarioCreadoEvento(Object source) {
        super(source);
    }
}

// Publicación del evento
@Component
public class UsuarioServicio {
    @Autowired
    private ApplicationEventPublisher eventPublisher;

    public void crearUsuario(Usuario usuario) {
        // 👤 Lógica para crear usuario
        eventPublisher.publishEvent(new UsuarioCreadoEvento(this));
    }
}

// Listener del evento
@Component
public class NotificacionListener {
    @EventListener
    public void manejarUsuarioCreado(UsuarioCreadoEvento evento) {
        // ✉️ Lógica para manejar el evento, como enviar una notificación
    }
}
```

## 6. Patrón Builder 🛠️

### ¿Qué Problema Resuelve?

El patrón Builder separa la construcción de un objeto complejo de su representación, permitiendo crear diferentes tipos y representaciones utilizando el mismo proceso de construcción. En Spring Boot, se utiliza comúnmente para construir objetos de configuración o solicitudes HTTP.

### ¿Cuándo Usarlo?

Cuando necesites crear objetos complejos con muchas opciones o parámetros, y quieras evitar constructores con muchos argumentos o múltiples setters.

**Ejemplo:**

```java
public class Usuario {
    private String nombre;
    private String email;
    private int edad;

    private Usuario(Builder builder) {
        this.nombre = builder.nombre;
        this.email = builder.email;
        this.edad = builder.edad;
    }

    public static class Builder {
        private String nombre;
        private String email;
        private int edad;

        public Builder nombre(String nombre) {
            this.nombre = nombre;
            return this;
        }

        public Builder email(String email) {
            this.email = email;
            return this;
        }

        public Builder edad(int edad) {
            this.edad = edad;
            return this;
        }

        public Usuario build() {
            return new Usuario(this);
        }
    }
}

// Uso del patrón Builder
Usuario usuario = new Usuario.Builder()
    .nombre("Juan")
    .email("juan@example.com")
    .edad(30)
    .build();
```

## 7. Patrón Fachada (Facade) 🏢

### ¿Qué Problema Resuelve?

El patrón Facade proporciona una interfaz simplificada a un subsistema complejo. En Spring Boot, esto se puede ver en la forma en que se encapsulan múltiples servicios o repositorios detrás de una única interfaz de servicio.

### ¿Cuándo Usarlo?

Cuando quieres simplificar la interacción con sistemas complejos o múltiples componentes, proporcionando una interfaz más amigable.

**Ejemplo:**

```java
@Service
public class PedidoService {
    @Autowired
    private InventarioService inventarioService;

    @Autowired
    private PagoService pagoService;

    public void procesarPedido(Pedido pedido) {
        inventarioService.reservarProductos(pedido);
        pagoService.procesarPago(pedido);
        // Más lógica 🛒
    }
}
```