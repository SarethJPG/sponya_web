// =========================================
// COMPORTAMIENTO DE LA NAVBAR AL HACER SCROLL
// =========================================

// Guardamos la posición inicial del scroll al cargar la página
let ubicacionPrincipal = window.scrollY || document.documentElement.scrollTop;

// Seleccionamos el elemento de la navbar por su ID
const navbar = document.getElementById('navbar');

// Añadimos un "escuchador de eventos" para detectar cada vez que se hace scroll
window.addEventListener('scroll', function() {
    
    // Obtenemos la posición actual del scroll en el momento del evento
    let desplazamientoActual = window.scrollY || document.documentElement.scrollTop;

    // Comparamos: si la posición anterior es mayor o igual a la actual, significa que estamos subiendo.
    // También validamos si el desplazamiento actual es 0 (estamos hasta arriba del todo).
    if (ubicacionPrincipal >= desplazamientoActual || desplazamientoActual <= 0) {
        // Al subir, quitamos la clase que oculta la navbar (la mostramos)
        navbar.classList.remove('navbar-oculta');
    } else {
        // Al bajar, agregamos la clase que oculta la navbar
        navbar.classList.add('navbar-oculta');
    }

    // Actualizamos la posición principal con la actual para la próxima vez que se ejecute el evento
    ubicacionPrincipal = desplazamientoActual;
});

// =========================================
// CARRUSEL DE BANNERS HERO (Bucle Infinito)
// =========================================

const track = document.getElementById('carrusel-track');
// Convertimos los hijos actuales del track en un Array (nuestras 3 cards originales)
const slidesOriginales = Array.from(track.children);
const btnIzq = document.getElementById('flecha-izq');
const btnDer = document.getElementById('flecha-der');
const puntos = Array.from(document.querySelectorAll('.punto'));

let indiceActual = 1; // Iniciamos en 1 porque en el índice 0 pondremos un clon de la última card
let enMovimiento = false; // Bandera para evitar que el usuario de clics rápidos y rompa la animación
let intervaloCarrusel; // Variable para guardar el temporizador

// 1. Crear los clones para la ilusión de carrusel infinito
const primerClon = slidesOriginales[0].cloneNode(true);
const ultimoClon = slidesOriginales[slidesOriginales.length - 1].cloneNode(true);

// 2. Insertar los clones en el DOM
track.appendChild(primerClon); // El clon de la card 1 se va al final (como si fuera la card 4)
track.insertBefore(ultimoClon, slidesOriginales[0]); // El clon de la card 3 se va al inicio (como si fuera la card 0)

// Actualizamos la lista de todas las cards (ahora son 5 en total con los clones)
const todosLosSlides = Array.from(track.children);

// 3. Función principal para desplazar la pista (track)
function moverCarrusel() {
    // Calculamos el ancho actual de una card
    const anchoSlide = todosLosSlides[0].getBoundingClientRect().width;
    // Movemos el contenedor entero hacia la izquierda multiplicando el ancho por el índice
    track.style.transform = `translateX(-${indiceActual * anchoSlide}px)`;
}

// Inicializamos la posición sin animación para que no se vea el salto al cargar la página
track.style.transition = 'none';
moverCarrusel();

// 4. Función para iluminar el punto (dot) correcto
function actualizarPuntos() {
    // Limpiamos todos los puntos
    puntos.forEach(punto => punto.classList.remove('activo'));
    
    // Calculamos qué punto encender en base al índice real
    let indexReal = indiceActual - 1;
    if (indiceActual === todosLosSlides.length - 1) indexReal = 0; // Si estamos viendo el clon del final
    if (indiceActual === 0) indexReal = slidesOriginales.length - 1; // Si estamos viendo el clon del inicio
    
    // Encendemos el punto correspondiente
    if(puntos[indexReal]) {
        puntos[indexReal].classList.add('activo');
    }
}

// 5. Funciones para navegar a los lados
function moverDerecha() {
    if (enMovimiento) return;
    enMovimiento = true;
    
    track.style.transition = 'transform 0.5s ease-in-out';
    indiceActual++;
    moverCarrusel();
    actualizarPuntos();
    reiniciarIntervalo();
}

function moverIzquierda() {
    if (enMovimiento) return;
    enMovimiento = true;
    
    track.style.transition = 'transform 0.5s ease-in-out';
    indiceActual--;
    moverCarrusel();
    actualizarPuntos();
    reiniciarIntervalo();
}

// 6. El "Efecto Ninja": Cuando termina la transición, revisamos si estamos en un clon
track.addEventListener('transitionend', () => {
    enMovimiento = false;
    
    // Si llegamos al clon de la derecha (la card 1 falsa)
    if (indiceActual === todosLosSlides.length - 1) {
        track.style.transition = 'none'; // Apagamos la animación
        indiceActual = 1; // Saltamos de regreso a la card 1 original
        moverCarrusel();
    }
    
    // Si llegamos al clon de la izquierda (la card 3 falsa)
    if (indiceActual === 0) {
        track.style.transition = 'none'; // Apagamos la animación
        indiceActual = todosLosSlides.length - 2; // Saltamos de regreso a la card 3 original
        moverCarrusel();
    }
});

// 7. Escuchadores de eventos para las flechas
btnDer.addEventListener('click', moverDerecha);
btnIzq.addEventListener('click', moverIzquierda);

// 8. Escuchadores de eventos para los puntos de la base
puntos.forEach((punto, index) => {
    punto.addEventListener('click', () => {
        if (enMovimiento) return;
        enMovimiento = true;
        
        // Sumamos 1 porque el índice de nuestro arreglo real está desfasado por el clon inicial
        indiceActual = index + 1;
        track.style.transition = 'transform 0.5s ease-in-out';
        moverCarrusel();
        actualizarPuntos();
        reiniciarIntervalo();
    });
});

// 9. Lógica del deslizamiento automático (cada 5000 milisegundos)
function iniciarIntervalo() {
    intervaloCarrusel = setInterval(moverDerecha, 50000);
}

// Reinicia el contador para que no salte de golpe si el usuario acaba de dar clic
function reiniciarIntervalo() {
    clearInterval(intervaloCarrusel);
    iniciarIntervalo();
}

// Arrancar el temporizador al cargar
iniciarIntervalo();

// 10. Reajustar la posición exacta si el usuario redimensiona la ventana
window.addEventListener('resize', () => {
    track.style.transition = 'none';
    moverCarrusel();
});


document.addEventListener("DOMContentLoaded", function() {
    const form = document.getElementById("form-contacto");

    if (form) {
        form.addEventListener("submit", function(e) {
            e.preventDefault(); // Detiene el envío por defecto para validar primero

            let isFormValid = true;

            // Función auxiliar para validar, sanear y mostrar errores
            const validateField = (id, regex) => {
                const input = document.getElementById(id);
                const errorMsg = input.nextElementSibling; // Selecciona el span de error debajo del input
                let value = input.value.trim();

                // 1. Filtro de seguridad: Elimina etiquetas HTML (<, >) para evitar inyección de scripts
                value = value.replace(/[<>]/g, "");
                input.value = value; // Devuelve el valor limpio al campo

                let isValid = true;

                // 2. Comprueba si está vacío o si falla la regla específica (Regex)
                if (value === "") {
                    isValid = false;
                } else if (regex && !regex.test(value)) {
                    isValid = false;
                }

                // 3. Aplica estilos visuales de error o éxito
                if (isValid) {
                    errorMsg.style.display = "none";
                    input.style.borderColor = "#d1d1d1"; // Vuelve al color original
                } else {
                    errorMsg.style.display = "block";
                    input.style.borderColor = "red"; // Pinta el borde de rojo
                    isFormValid = false;
                }
            };

            // Ejecutamos validaciones con Expresiones Regulares (Regex)
            // Nombre: Solo letras (incluyendo acentos y ñ) y espacios
            validateField("nombre", /^[a-zA-ZáéíóúÁÉÍÓÚñÑ\s]+$/);
            
            // Ciudad: Cualquier texto, pero no vacío
            validateField("ciudad", null);
            
            // Teléfono: Solo números y espacios
            validateField("telefono", /^[0-9\s]+$/);
            
            // Correo: Valida que contenga un "@" y algo después
            validateField("correo", /^.+@.+$/);
            
            // Mensaje: Cualquier texto, pero no vacío
            validateField("mensaje", null);

            // Si pasa todos los filtros de seguridad y reglas, envía por EmailJS
            if (isFormValid) {
                const templateParams = {
                    nombre: document.getElementById("nombre").value,
                    ciudad: document.getElementById("ciudad").value,
                    telefono: document.getElementById("telefono").value,
                    correo: document.getElementById("correo").value,
                    mensaje: document.getElementById("mensaje").value
                };

                emailjs.send('service_h3i2lva', 'template_ue12uqm', templateParams)
                    .then(function(response) {
                        alert("¡Mensaje enviado con éxito! Nos pondremos en contacto pronto.");
                        form.reset();
                    }, function(error) {
                        alert("Ocurrió un error al enviar el mensaje. Inténtalo de nuevo.");
                        console.log("FAILED...", error);
                    });
            }
        });
    }
});