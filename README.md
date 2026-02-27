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
    --secondary: #f39d12c0;
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
    max-width: 1000px;
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
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
    display: flex;
    flex-wrap: wrap;
    gap: 15px;
    margin-bottom: 20px;
    justify-content: center;
}

.group {
    display: flex;
    flex-direction: column;
}

input, button {
    padding: 10px;
    border-radius: 5px;
    border: 1px solid var(--border);
    margin-top: 5px;
}

button {
    background: var(--primary);
    color: white;
    border: none;
    cursor: pointer;
    font-weight: bold;
    align-self: flex-end;
}

button:hover { opacity: 0.9; }
button.reset { background: var(--danger); }

.simulation-area {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
}

.algorithm-panel {
    background: var(--card);
    padding: 15px;
    border-radius: 8px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.05);
}

h2 { margin-top: 0; font-size: 1.1rem; border-bottom: 2px solid var(--bg); padding-bottom: 10px; }

.memory-track {
    height: 350px;
    background: #e9ecef;
    border-radius: 5px;
    display: flex;
    flex-direction: column;
    gap: 6px;
    padding: 6px;
    overflow-y: auto;
}

.block {
    background: #bdc3c7;
    border: 1px solid #95a5a6;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 0.75rem;
    position: relative;
    transition: all 0.3s ease;
    min-height: 25px;
    color: #2c3e50;
    font-weight: 600;
}

.process-in-block {
    position: absolute;
    left: 0;
    top: 0;
    height: 100%;
    background: var(--primary);
    color: white;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
    animation: slideIn 0.5s ease-out;
}

/* Color diferenciador para Worst Fit */
#track-worst .process-in-block { background: var(--secondary); }

.stats {
    margin-top: 15px;
    font-size: 0.85rem;
    background: #f8f9fa;
    padding: 12px;
    border-radius: 5px;
    border-left: 4px solid var(--primary);
}

#track-worst + .stats { border-left-color: var(--secondary); }

@keyframes slideIn {
    from { width: 0; opacity: 0; }
    to { opacity: 1; }
}
```

#Script
```Java
let blocksBest = [];
let blocksWorst = [];
let countBest = 0;
let countWorst = 0;

window.onload = function() {
    console.log("Simulador vinculado correctamente.");
    
    init();
    document.getElementById('addBtn').onclick = addProcess;
    document.getElementById('resetBtn').onclick = resetSim;
};

function init() {
    const input = document.getElementById('blocksInput').value;

    const rawValue = input.trim() === "" ? "100, 500, 200, 300, 600" : input;
    
    const sizes = rawValue.split(',')
        .map(s => parseInt(s.trim()))
        .filter(n => !isNaN(n) && n > 0);
    
    blocksBest = sizes.map((s, i) => ({ id: i, total: s, remaining: s, processes: [] }));
    blocksWorst = sizes.map((s, i) => ({ id: i, total: s, remaining: s, processes: [] }));
    
    countBest = 0;
    countWorst = 0;
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
        const blockEl = document.createElement('div');
        blockEl.className = 'block';
       
        blockEl.style.height = Math.max(45, block.total / 1.5) + 'px'; 
        blockEl.style.border = "1px solid #555";
        blockEl.style.marginBottom = "8px";
        blockEl.style.position = "relative";
        blockEl.style.backgroundColor = "#2c3e50"; 
        blockEl.style.borderRadius = "4px";
        blockEl.style.overflow = "hidden";
        
        
        const infoText = document.createElement('span');
        infoText.innerHTML = `Libre: ${block.remaining}MB / ${block.total}MB`;
        infoText.style.position = "absolute";
        infoText.style.right = "10px"; 
        infoText.style.top = "50%";
        infoText.style.transform = "translateY(-50%)"; 
        infoText.style.zIndex = "10"; 
        infoText.style.color = "#fff";
        infoText.style.fontSize = "11px";
        infoText.style.fontWeight = "bold";
     
        infoText.style.textShadow = "0px 0px 4px rgba(0,0,0,0.9)";
        infoText.style.pointerEvents = "none"; 
        
        blockEl.appendChild(infoText);

        block.processes.forEach(p => {
            const pEl = document.createElement('div');
            pEl.className = 'process-in-block';
            
            pEl.style.width = (p.size / block.total * 100) + '%';
            pEl.style.height = "100%";
            pEl.style.position = "absolute";
            pEl.style.top = "0";
            pEl.style.left = "0";
            pEl.style.display = "flex";
            pEl.style.alignItems = "center";
            pEl.style.justifyContent = "center"; 
            pEl.style.color = "white";
            pEl.style.fontSize = "10px";
            pEl.style.fontWeight = "bold";
            pEl.style.zIndex = "5"; 
            
           
            pEl.style.backgroundColor = elementId === 'track-best' ? '#4a90e2' : '#e67e22';
            pEl.style.borderRight = "1px solid rgba(255,255,255,0.3)";
            
            pEl.innerText = `P${p.id}`;
            blockEl.appendChild(pEl);
        });

        track.appendChild(blockEl);
    });
}

function addProcess() {
    const sizeInput = document.getElementById('processSizeInput');
    const size = parseInt(sizeInput.value);
    
    if (isNaN(size) || size <= 0) {
        alert("Por favor, ingresa un tamaño de proceso válido.");
        return;
    }

 
    let bestIdx = -1;
    let minDiff = Infinity;
    blocksBest.forEach((b, i) => {
        if (b.remaining >= size && (b.remaining - size) < minDiff) {
            minDiff = b.remaining - size;
            bestIdx = i;
        }
    });

    if (bestIdx !== -1) {
        blocksBest[bestIdx].processes.push({ id: ++countBest, size: size });
        blocksBest[bestIdx].remaining -= size;
    }

    let worstIdx = -1;
    let maxDiff = -1;
    blocksWorst.forEach((b, i) => {
        if (b.remaining >= size && (b.remaining - size) > maxDiff) {
            maxDiff = b.remaining - size;
            worstIdx = i;
        }
    });

    if (worstIdx !== -1) {
        blocksWorst[worstIdx].processes.push({ id: ++countWorst, size: size });
        blocksWorst[worstIdx].remaining -= size;
    }

    render();
}

function updateStats() {
    const fragBest = blocksBest.reduce((acc, b) => acc + b.remaining, 0);
    const fragWorst = blocksWorst.reduce((acc, b) => acc + b.remaining, 0);

    const sBest = document.getElementById('stats-best');
    const sWorst = document.getElementById('stats-worst');

    if(sBest) sBest.innerHTML = `Fragmentación: ${fragBest} MB <br> Procesos: ${countBest}`;
    if(sWorst) sWorst.innerHTML = `Fragmentación: ${fragWorst} MB <br> Procesos: ${countWorst}`;
}

function resetSim() {
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
