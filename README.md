# -Puntos-adicionales-Simulador-de-HTML-
Durante esta practica se creo un HTML interactivo, donde el usuario tiene la oportunidad de practicar y aprender como funciona worst-fist y Best-first

#Introducción.
Este simulador fue desarrollado para representar de manera visual cómo funcionan los algoritmos de asignación de memoria Best Fit y Worst Fit en los sistemas operativos. Permite observar cómo se distribuyen los procesos dentro de bloques de memoria y cómo se genera la fragmentación dependiendo del algoritmo utilizado.

El objetivo principal es comparar ambos métodos y analizar cuál administra mejor el espacio disponible bajo distintas condiciones.


##Instrucciones paras interactuar.
¿Qué debe hacer el usuario?

Ingresar los tamaños de los bloques de memoria (o usar los valores predeterminados).

Escribir el tamaño del proceso que desea asignar.

Presionar el botón para agregar el proceso.

Repetir el proceso para observar cómo se comportan los algoritmos.

Puede reiniciar la simulación cuando lo desee.

¿Qué va a observar?

Cómo cada proceso se asigna a un bloque diferente según el algoritmo.

Cómo disminuye la memoria disponible en cada bloque.

La fragmentación total que queda después de varias asignaciones.

El número de procesos asignados. 

¿Qué significan los resultados?

Fragmentación: cantidad total de memoria libre que no ha sido utilizada.

Procesos: número de procesos que lograron asignarse.

La comparación visual muestra qué algoritmo aprovecha mejor la memoria en cada caso.

##Explicación de algoritmos.

* Best Fit

Selecciona el bloque donde el proceso quepa dejando la menor cantidad de espacio libre posible.
Busca reducir el desperdicio inmediato de memoria, aunque puede generar fragmentación pequeña acumulada.

* Worst Fit

Selecciona el bloque más grande disponible donde el proceso quepa.
La idea es dejar espacios grandes restantes para futuros procesos, pero puede desperdiciar más memoria en ciertos casos.

#Style
```Html
:root {
    --bg: #f0f2f5;
    --card: #ffffff;
    --primary: #1564be;
    --secondary: #d46b25; 
    --danger: #e74c3c;
    --text: #2c3e50;
    --border: #dcdde1;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background-color: var(--bg);
    color: var(--text);
    margin: 0;
    padding: 20px;
    display: flex;
    flex-direction: column;
    align-items: center;
}

.container {
    max-width: 1200px; 
    width: 100%;
}

header {
    text-align: center;
    margin-bottom: 20px;
}

.controls {
    background: var(--card);
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgb(153, 14, 177);
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    margin-bottom: 20px;
    justify-content: center;
    align-items: flex-end; 
}

.group {
    display: flex;
    flex-direction: column;
}

label {
    font-weight: bold;
    font-size: 0.9rem;
    margin-bottom: 5px;
}

input {
    padding: 10px;
    border-radius: 5px;
    border: 1px solid var(--border);
    min-width: 200px;
}

button {

    --bg: var(--primary); 
    --shadow: #1564be;
    padding: 10px 15px;
    border-radius: 5px;
    border: none;
    cursor: pointer;
    font-weight: bold;
    color: white;
    transition: transform 0.1s, box-shadow 0.2s;
    background-color: var(--bg);
    box-shadow: 0 2px 5px var(--shadow);
}

button:hover {
    transform: translateY(-2px);
    box-shadow: 0 6px 12px var(--shadow);
    filter: brightness(1.1); 
}

button:active {
    transform: translateY(0) scale(0.95);
    box-shadow: 0 2px 4px var(--shadow);
}

#resetBtn { 
    --bg: var(--danger);     
    --shadow: rgb(174, 41, 26); 
}

#addBlocBtn { 
    --bg: #44ad49;
    --shadow: rgba(56, 142, 60, 0.6); 
}

#randBtn { 
    --bg: #8e44ad;
    --shadow: rgba(170, 33, 220, 0.5); 
}

button:active { transform: scale(0.98); }
button.reset { background: var(--danger); margin-left: auto; }

.simulation-area {
    display: grid;
    grid-template-columns: 1fr 1fr; 
    gap: 20px;
    width: 100%;
}

@media (max-width: 768px) {
    .simulation-area { grid-template-columns: 1fr; }
}

.algorithm-panel {
    background: var(--card);
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgb(153, 14, 177);
    display: flex;
    flex-direction: column;
}

h2 {
    margin-top: 0;
    font-size: 1.2rem;
    text-align: center;
    color: var(--primary);
}

.stats {
    margin-bottom: 15px; 
    font-size: 0.9rem;
    background: #7598bc6a;
    padding: 10px;
    border-radius: 5px;
    border-left: 5px solid var(--primary);
    line-height: 1.5;
}

#stats-worst {
    background: #fdb85077;
    border-left-color: var(--secondary);
}

.memory-track {
    min-height: 300px;
    background: #e8e8e8;
    border-radius: 5px;
    padding: 10px;
    display: flex;
    flex-direction: column;
    gap: 10px;
}

.block {
    transition: all 0.3s ease;
}

@keyframes fadeIn {
    from { opacity: 0; transform: scaleY(0.8); }
    to { opacity: 1; transform: scaleY(1); }
}

.process-in-block {
    animation: fadeIn 0.3s ease-out;
}

.footer {
    margin-top: 10px;
    font-size: 0.8rem;
    color: var(--text);
    text-align: center;
    display: flex;
    flex-direction: column;
    gap: 1px;
    color: #0b33d3;
}
```

#Script
```Java
let blocksBest = [];
let blocksWorst = [];
let countBest = 0;
let countWorst = 0;

window.onload = function() {
    console.log("Simulador Listo.");
    
    // Inicializar
    init();

    // Event Listeners
    document.getElementById('addBtn').onclick = addProcess;
    document.getElementById('addBlocBtn').onclick = addBlocks;
    document.getElementById('resetBtn').onclick = resetSim;
    document.getElementById('randBtn').onclick = randomBlocks; 
};

function randomBlocks() {
    // Generar 3 bloques aleatorios entre 100MB y 800MB
    const randomSizes = [];
    for(let i = 0; i < 3; i++) {
        // Genera múltiplos de 50 para que sean números "bonitos"
        // Math.random() * (max - min) + min
        const size = Math.floor(Math.random() * 15 + 2) * 50; 
        randomSizes.push(size);
    }
    document.getElementById('blocksInput').value = randomSizes.join(', ');
    init();
} 
function addBlocks () {
    const input = document.getElementById('blocksInput').value;

    if (input.trim() === "") {
        alert("Ingresa un tamaño válido (Ej: 50, 150, 300).");
        return;
    } 
    init();
}

function init() {
    countBest = 0;
    countWorst = 0;

    const input = document.getElementById('blocksInput').value;
    const rawValue = input.trim() === "" ? "100, 500, 200, 300, 600" : input;
    
    const sizes = rawValue.split(',')
        .map(s => parseInt(s.trim()))
        .filter(n => !isNaN(n) && n > 0);

    if (sizes.length === 0) {
        alert("Configuración de bloques inválida.");
        return;
    }
    
    // Crear estructuras independientes
    blocksBest = sizes.map((s, i) => ({ id: i, total: s, remaining: s, processes: [] }));
    blocksWorst = sizes.map((s, i) => ({ id: i, total: s, remaining: s, processes: [] }));
    
    render();
}

function render() {
    renderTrack('track-best', blocksBest);
    renderTrack('track-worst', blocksWorst);
    updateStats();
}

function renderTrack(elementId, blocks) {
    const track = document.getElementById(elementId);
    if (!track) return;
    track.innerHTML = '';

    blocks.forEach(block => {
        // Contenedor visual del bloque
        const blockEl = document.createElement('div');
        blockEl.className = 'block';
        
        // Estilos base para el bloque
        blockEl.style.height = '50px'; 
        blockEl.style.width = '100%'; 
        blockEl.style.position = "relative";
        blockEl.style.backgroundColor = "#2c3e50"; 
        blockEl.style.border = "1px solid #fff";
        blockEl.style.borderRadius = "4px";
        blockEl.style.overflow = "hidden";
        blockEl.style.display = "flex"; // Flexbox para alinear procesos
        
        // Texto de información (centrado y flotante)
        const infoText = document.createElement('div');
        infoText.innerHTML = `Total: ${block.total} | <span style="color:#2ecc71">Libre: ${block.remaining}</span>`;
        infoText.style.position = "absolute";
        infoText.style.width = "100%";
        infoText.style.height = "100%";
        infoText.style.display = "flex";
        infoText.style.alignItems = "center";
        infoText.style.justifyContent = "center";
        infoText.style.zIndex = "10";
        infoText.style.color = "#fff";
        infoText.style.fontSize = "11px";
        infoText.style.fontWeight = "bold";
        infoText.style.textShadow = "1px 1px 2px black";
        infoText.style.pointerEvents = "none";
        
        blockEl.appendChild(infoText);

        // Renderizar procesos
        block.processes.forEach(p => {
            const pEl = document.createElement('div');
            
            // Ancho proporcional
            const widthPercent = (p.size / block.total) * 100;
            
            pEl.style.width = widthPercent + '%';
            pEl.style.height = "100%";
            // Color según algoritmo
            pEl.style.backgroundColor = elementId === 'track-best' ? '#3498db' : '#d35400'; 
            pEl.style.borderRight = "1px solid rgba(255,255,255,0.4)";
            pEl.style.boxSizing = "border-box";
            
            // Texto del proceso (P1, P2...)
            pEl.innerText = `P${p.id}`;
            pEl.style.display = "flex";
            pEl.style.alignItems = "center";
            pEl.style.justifyContent = "center";
            pEl.style.color = "white";
            pEl.style.fontSize = "10px";
            pEl.title = `Proceso ${p.id} (${p.size} MB)`;

            blockEl.appendChild(pEl);
        });

        track.appendChild(blockEl);
    });
}

function addProcess() {
    const sizeInput = document.getElementById('processSizeInput');
    const size = parseInt(sizeInput.value);


    // Validar si cabe en algún lado
    const canFitBest = blocksBest.some(b => b.remaining >= size);
    const canFitWorst = blocksWorst.some(b => b.remaining >= size);

    if (!canFitBest && !canFitWorst) {
        alert("No hay espacio suficiente en ningún bloque.");
        return;
    }

    // --- BEST FIT ---
    let bestIdx = -1;
    let minDiff = Infinity;
    blocksBest.forEach((b, i) => {
        if (b.remaining >= size) {
            let diff = b.remaining - size;
            if (diff < minDiff) {
                minDiff = diff;
                bestIdx = i;
            }
        }
    });
    if (bestIdx !== -1) {
        blocksBest[bestIdx].processes.push({ id: ++countBest, size: size });
        blocksBest[bestIdx].remaining -= size;
    }

    // --- WORST FIT ---
    let worstIdx = -1;
    let maxDiff = -1;
    blocksWorst.forEach((b, i) => {
        if (b.remaining >= size) {
            let diff = b.remaining - size;
            if (diff > maxDiff) {
                maxDiff = diff;
                worstIdx = i;
            }
        }
    });
    if (worstIdx !== -1) {
        blocksWorst[worstIdx].processes.push({ id: ++countWorst, size: size });
        blocksWorst[worstIdx].remaining -= size;
    }

    sizeInput.value = '';
    sizeInput.focus();
    render();
}

function updateStats() {
    const fragBest = blocksBest.reduce((acc, b) => acc + b.remaining, 0);
    const fragWorst = blocksWorst.reduce((acc, b) => acc + b.remaining, 0);

    const sBest = document.getElementById('stats-best');
    const sWorst = document.getElementById('stats-worst');

    if(sBest) sBest.innerHTML = `<strong>Fragmentación Total:</strong> ${fragBest} MB <br> <strong>Procesos:</strong> ${countBest}`;
    if(sWorst) sWorst.innerHTML = `<strong>Fragmentación Total:</strong> ${fragWorst} MB <br> <strong>Procesos:</strong> ${countWorst}`;
}

function resetSim() {
    document.getElementById('processSizeInput').value = '';
    init();
}

```
#imagen de programa ejecudandose.
<img width="1366" height="763" alt="image" src="https://github.com/user-attachments/assets/e49601c9-b8e8-4ee7-9007-fd66f2727547" />



#Reflexión

Este simulador permite comprender de forma práctica cómo las decisiones del sistema operativo influyen directamente en la eficiencia del uso de memoria. Se puede observar que no existe un algoritmo perfecto, ya que su rendimiento depende del tamaño y orden de llegada de los procesos.

La actividad ayuda a entender mejor el concepto de fragmentación y la importancia de elegir una estrategia adecuada de administración de memoria.
##Referencias.
Silberschatz, A., Galvin, P. B., & Gagne, G. (2018). Operating System Concepts.

Tanenbaum, A. S., & Bos, H. (2015). Modern Operating Systems.

Material de clase – Sistemas Operativos.


##Clausula de uso de Ai
Nosotros el equipo conformado por:
* Heidi Peña
* Erika Mendoza
* Cristopher Euan
Declaramos el no haber utilizado Ai durante la realización de este trabajo.  
