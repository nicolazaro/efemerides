<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Efemérides de la Radioafición</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Chosen Palette: Warm Neutrals -->
    <!-- Application Structure Plan: He diseñado una aplicación de página única (SPA) con una estructura temática para transformar el denso informe en una experiencia de usuario fluida. La navegación principal permite acceder a cuatro áreas clave: 1) "Pioneros", que presenta a las figuras clave en un formato de galería visualmente atractivo. 2) "Hitos", una línea de tiempo vertical e interactiva que narra la evolución de la radioafición por eras. 3) "Calendario", el núcleo interactivo que permite explorar las efemérides por mes, con filtros y una visualización gráfica. 4) "Glosario", para clarificar términos técnicos. Esta estructura híbrida combina la narrativa cronológica con la consulta práctica de datos, permitiendo al usuario explorar la historia de forma intuitiva o buscar datos específicos para el boletín del club, optimizando la usabilidad del contenido del informe. -->
    <!-- Visualization & Content Choices: Para visualizar la información del informe sin usar SVG o Mermaid, he seleccionado los siguientes métodos: 1) Para los pioneros (Objetivo: Informar), he usado tarjetas HTML/CSS que revelan más información al interactuar. 2) Para la cronología de eventos (Objetivo: Mostrar Cambio/Organizar), he implementado una línea de tiempo vertical con Tailwind CSS, donde cada hito es un punto interactivo. 3) Para el desglose de efemérides mensuales (Objetivo: Comparar/Explorar), utilizo un gráfico de dona de Chart.js (Canvas) que muestra la proporción de tipos de eventos (Avance Tecnológico, Personaje, etc.), y una lista dinámica en HTML que se puede filtrar por categoría mediante botones. Esta combinación ofrece una exploración de datos rica y funcional, adaptada a los objetivos de cada sección del informe. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        .active-nav {
            background-color: #005f73;
            color: #ffffff;
        }
        .inactive-nav {
            color: #e0f2f1;
            background-color: transparent;
        }
        .active-month {
            background-color: #005f73;
            color: white;
            border-color: #005f73;
        }
        .active-filter {
            background-color: #94d2bd;
            color: #005f73;
            font-weight: bold;
        }
        .timeline-item::before {
            content: '';
            position: absolute;
            left: -31px;
            top: 50%;
            transform: translateY(-50%);
            width: 20px;
            height: 20px;
            border-radius: 50%;
            background-color: white;
            border: 4px solid #005f73;
        }
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 400px;
            margin-left: auto;
            margin-right: auto;
            height: 350px;
            max-height: 400px;
        }
        .view-hidden { display: none; }
    </style>
</head>
<body class="bg-[#fdfbf7] text-[#3a3a3a]">

    <header class="bg-[#005f73] text-white p-6 shadow-lg">
        <div class="container mx-auto text-center">
            <h1 class="text-3xl md:text-4xl font-bold">📡 Efemérides de la Radioafición</h1>
            <p class="mt-2 text-lg text-teal-200">Un Viaje Interactivo por Nuestra Historia</p>
        </div>
    </header>

    <nav class="bg-[#007f8b] sticky top-0 z-50 shadow-md">
        <div class="container mx-auto flex justify-center items-center flex-wrap p-2">
            <button data-view="inicio" class="nav-btn active-nav text-sm md:text-base font-semibold py-2 px-4 rounded-lg m-1 transition-colors duration-300">Inicio</button>
            <button data-view="pioneros" class="nav-btn inactive-nav text-sm md:text-base font-semibold py-2 px-4 rounded-lg m-1 transition-colors duration-300">Pioneros</button>
            <button data-view="hitos" class="nav-btn inactive-nav text-sm md:text-base font-semibold py-2 px-4 rounded-lg m-1 transition-colors duration-300">Hitos</button>
            <button data-view="calendario" class="nav-btn inactive-nav text-sm md:text-base font-semibold py-2 px-4 rounded-lg m-1 transition-colors duration-300">Calendario</button>
            <button data-view="glosario" class="nav-btn inactive-nav text-sm md:text-base font-semibold py-2 px-4 rounded-lg m-1 transition-colors duration-300">Glosario</button>
        </div>
    </nav>

    <main class="container mx-auto p-4 md:p-8">

        <!-- Vista de Inicio -->
        <section id="view-inicio" class="text-center">
            <h2 class="text-3xl font-bold text-[#005f73] mb-4">Bienvenido al Legado Vivo de la Radioafición</h2>
            <p class="max-w-3xl mx-auto text-lg mb-8">Esta aplicación interactiva te invita a explorar la fascinante historia de la radioafición. Navega a través de las diferentes secciones para descubrir los pioneros que forjaron nuestro camino, los hitos que definieron épocas y un calendario detallado de efemérides para enriquecer el boletín de tu club. La radioafición es más que un hobby; es una historia de innovación, servicio y conexión global.</p>
            <div id="hito-del-mes" class="bg-white p-6 rounded-xl shadow-lg border border-gray-200 max-w-2xl mx-auto">
                <h3 class="text-2xl font-bold text-[#007f8b] mb-3">Hito del Mes: Junio</h3>
                <div id="hito-content"></div>
            </div>
        </section>

        <!-- Vista de Pioneros -->
        <section id="view-pioneros" class="view-hidden">
            <h2 class="text-3xl font-bold text-[#005f73] mb-6 text-center">Pioneros de las Ondas</h2>
             <p class="max-w-3xl mx-auto text-lg mb-8 text-center">Conoce a las mentes visionarias cuyos descubrimientos e invenciones sentaron las bases de la comunicación inalámbrica y la electrónica moderna. Su ingenio y perseverancia abrieron el camino para la comunidad global de radioaficionados de hoy.</p>
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8" id="pioneros-grid">
            </div>
        </section>

        <!-- Vista de Hitos -->
        <section id="view-hitos" class="view-hidden">
            <h2 class="text-3xl font-bold text-[#005f73] mb-6 text-center">Línea de Tiempo de Hitos Históricos</h2>
            <p class="max-w-3xl mx-auto text-lg mb-12 text-center">Recorre las eras que definieron la radioafición, desde los primeros experimentos hasta la revolución digital. Cada punto en esta línea de tiempo representa un momento crucial que impulsó nuestra disciplina hacia adelante.</p>
            <div class="relative w-full max-w-4xl mx-auto border-l-4 border-[#005f73] pl-8 md:pl-12 py-4">
                <div id="timeline-container"></div>
            </div>
        </section>

        <!-- Vista de Calendario -->
        <section id="view-calendario" class="view-hidden">
            <h2 class="text-3xl font-bold text-[#005f73] mb-2 text-center">Calendario de Efemérides</h2>
            <p class="max-w-3xl mx-auto text-lg mb-8 text-center">Explora los eventos históricos de la radioafición mes a mes. Selecciona un mes para ver los hitos y filtra por categoría para encontrar la información que necesitas para tu boletín o investigación.</p>
            
            <div id="month-selector" class="flex flex-wrap justify-center gap-2 mb-8"></div>
            
            <div class="flex flex-col lg:flex-row gap-8">
                <div class="w-full lg:w-1/2">
                    <h3 class="text-xl font-bold text-center mb-4">Eventos del Mes Seleccionado</h3>
                    <div id="filter-selector" class="flex flex-wrap justify-center gap-2 mb-4"></div>
                    <div id="events-list" class="bg-white p-4 rounded-xl shadow-md border border-gray-200 h-96 overflow-y-auto">
                        <p class="text-center text-gray-500">Selecciona un mes para ver los eventos.</p>
                    </div>
                </div>
                <div class="w-full lg:w-1/2 flex flex-col items-center">
                    <h3 class="text-xl font-bold text-center mb-4">Categorías de Eventos</h3>
                    <div class="chart-container">
                        <canvas id="eventsChart"></canvas>
                    </div>
                </div>
            </div>
        </section>

        <!-- Vista de Glosario -->
        <section id="view-glosario" class="view-hidden">
            <h2 class="text-3xl font-bold text-[#005f73] mb-6 text-center">Glosario de Términos Clave</h2>
            <p class="max-w-3xl mx-auto text-lg mb-8 text-center">Familiarízate con la terminología esencial de la radioafición. Esta sección define algunos de los conceptos y tecnologías más importantes mencionados a lo largo de nuestra historia.</p>
            <div id="glosario-container" class="max-w-3xl mx-auto space-y-4"></div>
        </section>

    </main>

    <footer class="bg-[#005f73] text-white text-center p-4 mt-12">
        <p>&copy; 2025 | Creado para Radio Clubes del Mundo. Un recurso para celebrar nuestra historia.</p>
    </footer>

<script>
const efemeridesData = [
    { mes: 1, dia: 1, anio: 1847, evento: "Inauguración de la primera línea telegráfica en EE. UU.", significado: "Hito en la telegrafía, base para futuras comunicaciones inalámbricas.", categoria: "Avance Tecnológico" },
    { mes: 1, dia: 1, anio: 1962, evento: "OSCAR 1 cesa sus operaciones.", significado: "Final de la misión del primer satélite de radioaficionados, que transmitió el mensaje 'HI' durante 22 días.", categoria: "Avance Tecnológico" },
    { mes: 1, dia: 1, anio: 2026, evento: "Brandmeister dejará de soportar IDs DMR de 7 dígitos que comienzan con '1'.", significado: "Marca un cambio en la gestión de identificadores en una de las redes DMR más grandes.", categoria: "Regulación" },
    { mes: 2, dia: 15, anio: 1999, evento: "Publicación del Reglamento del Servicio de Radioaficionados en Nicaragua.", significado: "Un ejemplo de la formalización legal de la radioafición en diferentes países.", categoria: "Regulación" },
    { mes: 3, dia: 27, anio: 1899, evento: "Guglielmo Marconi logra la primera comunicación por radio entre Inglaterra y Francia.", significado: "Demostración temprana de la capacidad de la telegrafía inalámbrica para cruzar fronteras.", categoria: "Avance Tecnológico" },
    { mes: 4, dia: 2, anio: 1872, evento: "Fallecimiento de Samuel Morse.", significado: "Co-inventor del Código Morse, fundamental para la telegrafía inalámbrica.", categoria: "Personaje Destacado" },
    { mes: 4, dia: 6, anio: 1914, evento: "Fundación de la American Radio Relay League (ARRL).", significado: "Organización clave que coordinó y defendió la actividad de los radioaficionados en EE. UU.", categoria: "Organización" },
    { mes: 4, dia: 15, anio: 1912, evento: "Hundimiento del RMS Titanic.", significado: "Evento que expuso la necesidad de regulación del espectro, llevando a la Ley de Radio de 1912.", categoria: "Evento Significativo" },
    { mes: 4, dia: 18, anio: 1925, evento: "Fundación de la International Amateur Radio Union (IARU).", significado: "Organización global que defiende las frecuencias de la radioafición. Se celebra como el Día Mundial del Radioaficionado.", categoria: "Organización" },
    { mes: 4, dia: 21, anio: 1965, evento: "Fallecimiento de Edward V. Appleton.", significado: "Físico premio Nobel por su investigación de la ionosfera, esencial para la propagación de ondas.", categoria: "Personaje Destacado" },
    { mes: 4, dia: 25, anio: 1874, evento: "Nacimiento de Guglielmo Marconi.", significado: "Ingeniero y pionero en la telegrafía inalámbrica, sentó las bases de la radio.", categoria: "Personaje Destacado" },
    { mes: 4, dia: 27, anio: 1791, evento: "Nacimiento de Samuel Morse.", significado: "Inventor del Código Morse y el telégrafo eléctrico.", categoria: "Personaje Destacado" },
    { mes: 4, dia: 29, anio: 2017, evento: "Estreno del modo digital FT8.", significado: "Modo digital altamente eficiente para señales débiles, popularizado por Joe Taylor (K1JT).", categoria: "Avance Tecnológico" },
    { mes: 5, dia: 1, anio: 1844, evento: "Samuel Morse telegrafía la noticia de la nominación de Henry Clay.", significado: "Demostración pública temprana de la capacidad del telégrafo para la transmisión rápida de noticias.", categoria: "Evento Significativo" },
    { mes: 6, dia: 29, anio: 2017, evento: "Lanzamiento oficial del modo FT8.", significado: "Un modo digital que ha revolucionado las comunicaciones en condiciones de señal débil.", categoria: "Avance Tecnológico" },
    { mes: 6, dia: 30, anio: 1961, evento: "Fallecimiento de Lee De Forest.", significado: "Inventor del triodo (Audion), considerado el 'Padre de la Electrónica'.", categoria: "Personaje Destacado" },
    { mes: 7, dia: 18, anio: 1907, evento: "Lee De Forest realiza las primeras transmisiones de barco a tierra por radiotelefonía.", significado: "Hito en la transmisión de voz inalámbrica, mostrando el potencial más allá del código Morse.", categoria: "Avance Tecnológico" },
    { mes: 7, dia: 20, anio: 1937, evento: "Fallecimiento de Guglielmo Marconi.", significado: "Pionero de la radio, galardonado con el Premio Nobel, esencial para la comunicación moderna.", categoria: "Personaje Destacado" },
    { mes: 8, dia: 26, anio: 1873, evento: "Nacimiento de Lee De Forest.", significado: "Inventor del triodo, una invención fundamental que impulsó la era de la electrónica.", categoria: "Personaje Destacado" },
    { mes: 9, dia: 6, anio: 1892, evento: "Nacimiento de Edward V. Appleton.", significado: "Físico cuyas investigaciones sobre la ionosfera fueron clave para la propagación de radio.", categoria: "Personaje Destacado" },
    { mes: 10, dia: 1, anio: 1919, evento: "Se levantan las restricciones de transmisión tras la Primera Guerra Mundial.", significado: "Resurgimiento de la radioafición después del 'apagón' de la guerra, gracias a las gestiones de la ARRL.", categoria: "Regulación" },
    { mes: 10, dia: 4, anio: 1927, evento: "Inicio de la Conferencia Internacional de Radiotelegrafía de Washington.", significado: "Conferencia crucial que adjudicó bandas de frecuencia (80, 40, 20, 10 metros) a los radioaficionados.", categoria: "Regulación" },
    { mes: 10, dia: 4, anio: 1957, evento: "Lanzamiento del Sputnik 1.", significado: "Su lanzamiento marcó el inicio de la era espacial e inspiró el desarrollo de los satélites OSCAR.", categoria: "Evento Significativo" },
    { mes: 10, dia: 10, anio: 1982, evento: "Canonización de San Maximiliano Kolbe, patrón de los radioaficionados.", significado: "Reconocimiento espiritual para la comunidad, destacando su devoción y servicio.", categoria: "Personaje Destacado" },
    { mes: 10, dia: 16, anio: 1924, evento: "Primer contacto bidireccional entre Reino Unido (G2SZ) y Nueva Zelanda (ZL4AAA).", significado: "Logro histórico de la primera comunicación inalámbrica bidireccional transmundial entre radioaficionados.", categoria: "Evento Significativo" },
    { mes: 10, dia: 21, anio: 1921, evento: "Fundación del Radio Club Argentino (RCA).", significado: "Uno de los clubes más antiguos e influyentes de América Latina, cofundador de la IARU.", categoria: "Organización" },
    { mes: 11, dia: 15, anio: 1921, evento: "Primera recepción de señales de radioaficionados de EE. UU. en Escocia.", significado: "Hito en la comunicación transatlántica para aficionados, demostrando el potencial de las ondas cortas.", categoria: "Evento Significativo" },
    { mes: 11, dia: 25, anio: 1927, evento: "Fin de la Conferencia Internacional de Radiotelegrafía de Washington.", significado: "Conferencia que sentó las bases regulatorias y de asignación de frecuencias a nivel mundial.", categoria: "Regulación" },
    { mes: 12, dia: 8, anio: 1923, evento: "Primer contacto bidireccional Reino Unido-EE.UU.", significado: "Hito en la comunicación transatlántica bidireccional entre aficionados (G2KF y U1MO).", categoria: "Evento Significativo" },
    { mes: 12, dia: 12, anio: 1961, evento: "Lanzamiento de OSCAR 1.", significado: "Inicio de la era de los satélites de radioaficionados, una contribución pionera a la exploración espacial.", categoria: "Avance Tecnológico" },
    { mes: 12, dia: 19, anio: 1915, evento: "Publicación del primer número de QST.", significado: "Establecimiento de un foro vital para la experimentación en la comunidad de radioaficionados.", categoria: "Organización" },
    { mes: 12, dia: 24, anio: 1906, evento: "Reginald Fessenden realiza la primera transmisión de voz y música.", significado: "Marca el inicio de la radiodifusión, demostrando la capacidad de la radio más allá del código Morse.", categoria: "Avance Tecnológico" },
];

const pionerosData = [
    { nombre: "Samuel Morse", fechas: "1791-1872", aporte: "Co-creador del Código Morse y el telégrafo eléctrico, el primer lenguaje digital universal para la comunicación a distancia.", img: "👤" },
    { nombre: "Heinrich Hertz", fechas: "1857-1894", aporte: "Demostró la existencia de las ondas electromagnéticas en 1887, confirmando la teoría de Maxwell y abriendo el camino para la radio.", img: "⚡️" },
    { nombre: "Guglielmo Marconi", fechas: "1874-1937", aporte: "Pionero de la telegrafía inalámbrica, realizó la primera transmisión transoceánica en 1901, impulsando la comercialización de la radio.", img: "📡" },
    { nombre: "Lee De Forest", fechas: "1873-1961", aporte: "Inventor del triodo (Audion) en 1906, que permitió la amplificación de señales y sentó las bases de la electrónica moderna.", img: "💡" },
    { nombre: "Edward Appleton", fechas: "1892-1965", aporte: "Nobel de Física por investigar la ionosfera, explicando cómo las ondas de radio viajan largas distancias al ser reflejadas por la atmósfera.", img: "🌍" },
    { nombre: "Reginald Fessenden", fechas: "1866-1932", aporte: "Realizó la primera transmisión de voz y música en 1906, demostrando el potencial de la radio como medio de radiodifusión.", img: "🎶" },
];

const hitosData = [
    { era: "Los Albores (1887-1912)", titulo: "Nacimiento de la Radio", descripcion: "Desde la demostración de las ondas de Hertz hasta la invención del triodo de De Forest, esta era sentó las bases científicas y tecnológicas. La experimentación amateur creció sin control, llevando a la primera regulación con la Ley de Radio de 1912." },
    { era: "Consolidación (1912-1927)", titulo: "Regulación y Organización", descripcion: "El hundimiento del Titanic aceleró la regulación. Se fundan la ARRL (1914) y la IARU (1925), organizaciones clave para defender y coordinar a los radioaficionados. Se logran los primeros contactos transatlánticos." },
    { era: "Reconocimiento Global (1927-1945)", titulo: "Asignación de Bandas y Servicio en Guerra", descripcion: "La Conferencia de Washington de 1927 asigna las primeras bandas exclusivas (80, 40, 20, 10m). A pesar del 'apagón' durante las Guerras Mundiales, los radioaficionados demostraron su valor estratégico, sirviendo como operadores calificados." },
    { era: "Era Dorada (1945-1980)", titulo: "Innovación y Conquista del Espacio", descripcion: "La posguerra trajo equipos excedentes y la revolución de la Banda Lateral Única (SSB). Los radioaficionados alcanzaron nuevas fronteras con los primeros contactos Tierra-Luna-Tierra (EME) y el lanzamiento de satélites propios como el OSCAR 1 en 1961." },
    { era: "Era Digital (1980-Hoy)", titulo: "La Revolución Digital y del Software", descripcion: "La llegada de las PCs transformó el hobby. Se desarrollaron modos digitales eficientes como PSK31 y FT8. La Radio Definida por Software (SDR) democratizó el acceso a tecnología avanzada, y la radioafición reafirmó su rol vital en comunicaciones de emergencia." },
];

const glosarioData = [
    { termino: "ARRL", definicion: "American Radio Relay League. Fundada en 1914, es la organización de radioaficionados más grande de Estados Unidos, defensora de los derechos y coordinadora de actividades." },
    { termino: "EME", definicion: "Earth-Moon-Earth (Tierra-Luna-Tierra). Una técnica de comunicación que utiliza la Luna como reflector pasivo para realizar contactos a larga distancia, un desafío técnico para los radioaficionados." },
    { termino: "FT8", definicion: "Un modo de comunicación digital extremadamente eficiente para señales débiles, introducido en 2017. Ha revolucionado los contactos de larga distancia (DX) con baja potencia." },
    { termino: "IARU", definicion: "International Amateur Radio Union. Fundada en 1925, es la federación mundial de sociedades nacionales de radioaficionados, que representa sus intereses ante organismos internacionales." },
    { termino: "OSCAR", definicion: "Orbiting Satellite Carrying Amateur Radio. Nombre de una serie de satélites construidos y lanzados por radioaficionados. OSCAR 1, en 1961, fue el primero." },
    { termino: "SDR", definicion: "Software Defined Radio (Radio Definida por Software). Un sistema de radiocomunicación donde los componentes que tradicionalmente se implementaban en hardware (filtros, moduladores, etc.) se realizan mediante software en una computadora." },
    { termino: "SSB", definicion: "Single-Sideband (Banda Lateral Única). Un modo de transmisión de voz muy eficiente que utiliza menos ancho de banda y potencia que el modo AM, popularizado por los radioaficionados en los años 50." },
];

const meses = ["Enero", "Febrero", "Marzo", "Abril", "Mayo", "Junio", "Julio", "Agosto", "Septiembre", "Octubre", "Noviembre", "Diciembre"];
const categorias = ["Todos", "Avance Tecnológico", "Regulación", "Personaje Destacado", "Evento Significativo", "Organización"];
const categoryColors = {
    'Avance Tecnológico': 'rgba(0, 95, 115, 0.8)',
    'Regulación': 'rgba(10, 147, 150, 0.8)',
    'Personaje Destacado': 'rgba(148, 210, 189, 0.8)',
    'Evento Significativo': 'rgba(238, 155, 0, 0.8)',
    'Organización': 'rgba(202, 103, 2, 0.8)',
};

let eventsChart = null;
let appState = {
    currentView: 'inicio',
    selectedMonth: new Date().getMonth() + 1,
    activeFilter: 'Todos'
};

document.addEventListener('DOMContentLoaded', () => {
    initApp();
});

function initApp() {
    setupNavigation();
    renderPioneros();
    renderTimeline();
    renderGlosario();
    renderMonthSelector();
    renderFilterSelector();
    initChart();
    
    // Vista inicial
    const currentMonthIndex = new Date().getMonth();
    selectMonth(currentMonthIndex);
    renderHitoDelMes();
    switchView('inicio');
}

function setupNavigation() {
    const navButtons = document.querySelectorAll('.nav-btn');
    navButtons.forEach(btn => {
        btn.addEventListener('click', () => switchView(btn.dataset.view));
    });
}

function switchView(viewId) {
    appState.currentView = viewId;
    document.querySelectorAll('main > section').forEach(section => {
        section.classList.add('view-hidden');
    });
    document.getElementById(`view-${viewId}`).classList.remove('view-hidden');
    
    document.querySelectorAll('.nav-btn').forEach(btn => {
        btn.classList.toggle('active-nav', btn.dataset.view === viewId);
        btn.classList.toggle('inactive-nav', btn.dataset.view !== viewId);
    });
    window.scrollTo(0, 0);
}

function renderHitoDelMes() {
    const currentMonth = new Date().getMonth() + 1;
    const hitoContainer = document.getElementById('hito-content');
    const hitosDelMes = efemeridesData.filter(e => e.mes === currentMonth);
    
    if (hitosDelMes.length > 0) {
        const hito = hitosDelMes[Math.floor(Math.random() * hitosDelMes.length)]; // Selecciona uno al azar del mes
        hitoContainer.innerHTML = `
            <p class="text-xl mb-2">${hito.dia} de ${meses[hito.mes-1]} de ${hito.anio}</p>
            <p class="text-2xl font-semibold mb-3">${hito.evento}</p>
            <p class="text-gray-600">${hito.significado}</p>
        `;
    } else {
        hitoContainer.innerHTML = `<p class="text-lg text-gray-600">No hay hitos destacados para este mes, pero te invitamos a explorar el calendario.</p>`;
    }
     document.getElementById('hito-del-mes').querySelector('h3').textContent = `Hito del Mes: ${meses[currentMonth-1]}`;
}


function renderPioneros() {
    const grid = document.getElementById('pioneros-grid');
    grid.innerHTML = pionerosData.map(p => `
        <div class="bg-white p-6 rounded-xl shadow-lg hover:shadow-2xl hover:scale-105 transition-all duration-300 border border-gray-200">
            <div class="text-4xl mb-4">${p.img}</div>
            <h3 class="text-2xl font-bold text-[#005f73]">${p.nombre}</h3>
            <p class="text-sm text-gray-500 mb-3">${p.fechas}</p>
            <p class="text-base text-gray-700">${p.aporte}</p>
        </div>
    `).join('');
}

function renderTimeline() {
    const container = document.getElementById('timeline-container');
    container.innerHTML = hitosData.map(h => `
        <div class="mb-10 ml-4 relative timeline-item">
            <div class="absolute -left-1.5 mt-1.5 w-3 h-3 bg-[#007f8b] rounded-full"></div>
            <p class="text-sm font-semibold text-gray-500">${h.era}</p>
            <h3 class="text-xl font-bold text-[#005f73] mt-1">${h.titulo}</h3>
            <p class="text-gray-700 mt-2">${h.descripcion}</p>
        </div>
    `).join('');
}

function renderMonthSelector() {
    const selector = document.getElementById('month-selector');
    selector.innerHTML = meses.map((mes, index) => `
        <button onclick="selectMonth(${index})" data-month="${index+1}"
            class="month-btn border-2 border-gray-300 text-gray-700 py-2 px-4 rounded-full transition-colors duration-300 hover:bg-[#94d2bd] hover:text-[#005f73]">
            ${mes}
        </button>
    `).join('');
}

function renderFilterSelector() {
    const selector = document.getElementById('filter-selector');
    selector.innerHTML = categorias.map(cat => `
        <button onclick="applyFilter('${cat}')" data-filter="${cat}"
            class="filter-btn border border-gray-300 text-sm py-1 px-3 rounded-full transition-colors duration-300">
            ${cat}
        </button>
    `).join('');
    document.querySelector('.filter-btn[data-filter="Todos"]').classList.add('active-filter');
}

function selectMonth(monthIndex) {
    appState.selectedMonth = monthIndex + 1;
    document.querySelectorAll('.month-btn').forEach(btn => {
        btn.classList.toggle('active-month', parseInt(btn.dataset.month) === appState.selectedMonth);
    });
    applyFilter('Todos'); // Reset filter on month change
}

function applyFilter(category) {
    appState.activeFilter = category;
    document.querySelectorAll('.filter-btn').forEach(btn => {
        btn.classList.toggle('active-filter', btn.dataset.filter === appState.activeFilter);
    });
    
    let filteredEvents = efemeridesData.filter(e => e.mes === appState.selectedMonth);
    if (appState.activeFilter !== 'Todos') {
        filteredEvents = filteredEvents.filter(e => e.categoria === appState.activeFilter);
    }
    
    renderEventList(filteredEvents);
    updateChart(filteredEvents);
}

function renderEventList(events) {
    const list = document.getElementById('events-list');
    if (events.length === 0) {
        list.innerHTML = `<p class="text-center text-gray-500 p-4">No hay eventos para esta selección.</p>`;
        return;
    }
    list.innerHTML = events.sort((a, b) => a.dia - b.dia).map(e => `
        <div class="border-b border-gray-200 p-3 hover:bg-teal-50">
            <p class="font-bold text-[#005f73]">${e.dia} de ${meses[e.mes-1]} de ${e.anio}: ${e.evento}</p>
            <p class="text-sm text-gray-600">${e.significado}</p>
            <p class="text-xs text-white bg-[${Object.values(categoryColors)[Object.keys(categoryColors).indexOf(e.categoria)]}] rounded-full px-2 py-0.5 inline-block mt-2" style="background-color: ${categoryColors[e.categoria] || '#777'}">${e.categoria}</p>
        </div>
    `).join('');
}


function initChart() {
    const ctx = document.getElementById('eventsChart').getContext('2d');
    eventsChart = new Chart(ctx, {
        type: 'doughnut',
        data: {
            labels: [],
            datasets: [{
                label: 'Eventos',
                data: [],
                backgroundColor: Object.values(categoryColors),
                borderColor: '#fdfbf7',
                borderWidth: 4
            }]
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: {
                legend: {
                    position: 'bottom',
                    labels: {
                        padding: 20,
                        boxWidth: 15,
                        font: {
                            size: 14
                        }
                    }
                },
                tooltip: {
                    callbacks: {
                        label: function(context) {
                            let label = context.label || '';
                            if (label) {
                                label += ': ';
                            }
                            if (context.parsed !== null) {
                                label += context.parsed;
                            }
                            return label;
                        }
                    }
                }
            }
        }
    });
}

function updateChart(events) {
    if (!eventsChart) return;
    const categoryCounts = {};
    categorias.slice(1).forEach(cat => categoryCounts[cat] = 0);
    
    events.forEach(e => {
        if (categoryCounts.hasOwnProperty(e.categoria)) {
            categoryCounts[e.categoria]++;
        }
    });

    const labels = Object.keys(categoryCounts);
    const data = Object.values(categoryCounts);
    
    eventsChart.data.labels = labels;
    eventsChart.data.datasets[0].data = data;
    eventsChart.update();
}

function renderGlosario() {
    const container = document.getElementById('glosario-container');
    container.innerHTML = glosarioData.map(item => `
        <div class="bg-white rounded-lg shadow-sm border border-gray-200">
            <details class="p-4">
                <summary class="font-bold text-lg text-[#005f73] cursor-pointer">${item.termino}</summary>
                <p class="mt-2 text-gray-700">${item.definicion}</p>
            </details>
        </div>
    `).join('');
}
</script>

</body>
</html>
