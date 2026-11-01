# MetodosG2
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Análisis Numérico - Métodos Selección Manual</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Math.js for mathematical operations -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjs/11.8.0/math.js"></script>
    <!-- Chart.js for simple static plotting -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { font-family: system-ui, -apple-system, sans-serif; }
    </style>
</head>
<body class="bg-slate-100 text-slate-800 min-h-screen p-4 md:p-8">

    <div class="max-w-4xl mx-auto space-y-6">
        
        <!-- Header -->
        <header class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 text-center">
            <h1 class="text-2xl font-bold text-slate-800">Calculadora de Métodos Numéricos</h1>
            <p class="text-sm text-slate-500 mt-1">Interpolación y Aproximación de Raíces</p>
        </header>

        <!-- Main Form Grid -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">

            <!-- Configuration & Selector -->
            <div class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 space-y-4">
                <h2 class="text-lg font-semibold text-slate-700">1. Selección de Método</h2>
                
                <div>
                    <label class="block text-xs font-medium text-slate-500 uppercase mb-1">Categoría</label>
                    <select id="categorySelect" class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2.5 text-sm focus:ring-2 focus:ring-blue-500 outline-none">
                        <option value="interpolation">Interpolación</option>
                        <option value="roots">Aproximación de Raíces</option>
                    </select>
                </div>

                <div>
                    <label class="block text-xs font-medium text-slate-500 uppercase mb-1">Método</label>
                    <select id="methodSelect" class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2.5 text-sm focus:ring-2 focus:ring-blue-500 outline-none">
                        <!-- Dynamic Options -->
                    </select>
                </div>

                <!-- Root Finding Inputs (Hidden by default for interpolation) -->
                <div id="rootsInputContainer" class="hidden space-y-3 pt-2 border-t border-slate-100">
                    <div>
                        <label class="block text-xs font-medium text-slate-500 uppercase mb-1">Función f(x)</label>
                        <input type="text" id="fxInput" value="x^3 - x - 2" class="w-full font-mono bg-slate-50 border border-slate-300 rounded-lg p-2 text-sm">
                    </div>
                    <div class="grid grid-cols-2 gap-2">
                        <div>
                            <label id="lblX0" class="block text-xs font-medium text-slate-500 mb-1">x0 / Lim A</label>
                            <input type="number" id="x0Input" value="1" step="any" class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2 text-sm">
                        </div>
                        <div id="wrapperX1">
                            <label id="lblX1" class="block text-xs font-medium text-slate-500 mb-1">x1 / Lim B</label>
                            <input type="number" id="x1Input" value="2" step="any" class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2 text-sm">
                        </div>
                    </div>
                    <div class="grid grid-cols-2 gap-2">
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">Tolerancia</label>
                            <input type="number" id="tolInput" value="0.0001" step="any" class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2 text-sm">
                        </div>
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">Max Iter</label>
                            <input type="number" id="maxIterInput" value="50" class="w-full bg-slate-50 border border-slate-300 rounded-lg p-2 text-sm">
                        </div>
                    </div>
                </div>

                <button id="calcBtn" class="w-full py-3 bg-blue-600 hover:bg-blue-700 text-white font-semibold rounded-xl shadow transition">
                    Calcular
                </button>
            </div>

            <!-- Interpolation Points Table -->
            <div id="tableContainer" class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 space-y-4">
                <div class="flex justify-between items-center">
                    <h2 class="text-lg font-semibold text-slate-700">2. Tabla de Datos (x, y)</h2>
                    <button id="addRowBtn" class="text-xs bg-slate-100 hover:bg-slate-200 text-slate-700 font-medium px-3 py-1.5 rounded-lg transition">
                        + Fila
                    </button>
                </div>

                <div class="max-h-56 overflow-y-auto border border-slate-200 rounded-lg">
                    <table class="w-full text-left text-sm">
                        <thead class="bg-slate-50 text-slate-500 text-xs uppercase border-b border-slate-200">
                            <tr>
                                <th class="p-2 text-center">#</th>
                                <th class="p-2">X</th>
                                <th class="p-2">Y</th>
                                <th class="p-2 text-center">Acción</th>
                            </tr>
                        </thead>
                        <tbody id="tableBody" class="divide-y divide-slate-100">
                            <!-- Dynamic Rows -->
                        </tbody>
                    </table>
                </div>
            </div>

        </div>

        <!-- Output & Chart Box -->
        <div class="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 space-y-6">
            <div>
                <h2 class="text-lg font-semibold text-slate-700 mb-2">Resultado</h2>
                <div id="resultOutput" class="p-4 bg-slate-50 border border-slate-200 rounded-xl font-mono text-sm min-h-[50px] flex items-center">
                    Selecciona los parámetros y presiona "Calcular".
                </div>
            </div>

            <div>
                <h2 class="text-lg font-semibold text-slate-700 mb-2">Gráfico</h2>
                <div class="relative h-64 w-full">
                    <canvas id="chartCanvas"></canvas>
                </div>
            </div>
        </div>

    </div>

    <!-- Application Logic -->
    <script>
        // State
        const points = [
            { x: 0, y: 1 },
            { x: 1, y: 3 },
            { x: 2, y: 2 },
            { x: 3, y: 5 }
        ];

        const methods = {
            interpolation: [
                { id: 'newton', name: 'Diferencias Divididas de Newton' },
                { id: 'splines', name: 'Splines Cúbicos Naturales' }
            ],
            roots: [
                { id: 'bisection', name: 'Bisección' },
                { id: 'newton_raphson', name: 'Newton-Raphson' },
                { id: 'secant', name: 'Secante' }
            ]
        };

        // DOM elements
        const categorySelect = document.getElementById('categorySelect');
        const methodSelect = document.getElementById('methodSelect');
        const tableContainer = document.getElementById('tableContainer');
        const rootsInputContainer = document.getElementById('rootsInputContainer');
        const tableBody = document.getElementById('tableBody');
        const addRowBtn = document.getElementById('addRowBtn');
        const calcBtn = document.getElementById('calcBtn');
        const resultOutput = document.getElementById('resultOutput');
        const wrapperX1 = document.getElementById('wrapperX1');
        const lblX0 = document.getElementById('lblX0');

        let chartInstance = null;

        // UI Handlers
        function updateMethods() {
            const cat = categorySelect.value;
            methodSelect.innerHTML = '';
            methods[cat].forEach(m => {
                const opt = document.createElement('option');
                opt.value = m.id;
                opt.textContent = m.name;
                methodSelect.appendChild(opt);
            });

            if (cat === 'interpolation') {
                tableContainer.classList.remove('hidden');
                rootsInputContainer.classList.add('hidden');
            } else {
                tableContainer.classList.add('hidden');
                rootsInputContainer.classList.remove('hidden');
                updateRootInputs();
            }
        }

        function updateRootInputs() {
            const method = methodSelect.value;
            if (method === 'newton_raphson') {
                wrapperX1.classList.add('hidden');
                lblX0.textContent = 'x0 (Valor Inicial)';
            } else {
                wrapperX1.classList.remove('hidden');
                lblX0.textContent = method === 'bisection' ? 'Lim A' : 'x0';
            }
        }

        function renderTable() {
            tableBody.innerHTML = '';
            points.forEach((pt, index) => {
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="p-2 text-center text-slate-400 font-mono text-xs">${index + 1}</td>
                    <td class="p-1"><input type="number" step="any" value="${pt.x}" class="w-full bg-slate-50 border rounded p-1 text-sm font-mono text-center" onchange="points[${index}].x = parseFloat(this.value)||0"></td>
                    <td class="p-1"><input type="number" step="any" value="${pt.y}" class="w-full bg-slate-50 border rounded p-1 text-sm font-mono text-center" onchange="points[${index}].y = parseFloat(this.value)||0"></td>
                    <td class="p-1 text-center">
                        <button onclick="removeRow(${index})" class="text-rose-500 hover:text-rose-700 font-bold px-2">✕</button>
                    </td>
                `;
                tableBody.appendChild(tr);
            });
        }

        function removeRow(idx) {
            if (points.length > 2) {
                points.splice(idx, 1);
                renderTable();
            }
        }

        addRowBtn.addEventListener('click', () => {
            const lastX = points.length > 0 ? points[points.length - 1].x : 0;
            points.push({ x: lastX + 1, y: 0 });
            renderTable();
        });

        categorySelect.addEventListener('change', updateMethods);
        methodSelect.addEventListener('change', updateRootInputs);

        // --- Numerical Methods ---

        // 1. Newton Interpolation
        function calcNewtonInterpolation() {
            const pts = [...points].sort((a, b) => a.x - b.x);
            const n = pts.length;
            const F = Array.from({ length: n }, () => Array(n).fill(0));

            for (let i = 0; i < n; i++) F[i][0] = pts[i].y;

            for (let j = 1; j < n; j++) {
                for (let i = 0; i < n - j; i++) {
                    F[i][j] = (F[i + 1][j - 1] - F[i][j - 1]) / (pts[i + j].x - pts[i].x);
                }
            }

            const coefs = F[0];
            const polyFn = (x) => {
                let y = coefs[0];
                for (let i = 1; i < n; i++) {
                    let term = coefs[i];
                    for (let k = 0; k < i; k++) term *= (x - pts[k].x);
                    y += term;
                }
                return y;
            };

            // Expression string construction
            let str = `${coefs[0].toFixed(3)}`;
            for (let i = 1; i < n; i++) {
                const sign = coefs[i] >= 0 ? ' + ' : ' - ';
                let term = `${Math.abs(coefs[i]).toFixed(3)}`;
                for (let k = 0; k < i; k++) {
                    term += `(x ${pts[k].x >= 0 ? '-' : '+'} ${Math.abs(pts[k].x)})`;
                }
                str += sign + term;
            }

            return { text: `P(x) = ${str}`, fn: polyFn, pts };
        }

        // 2. Cubic Spline Interpolation
        function calcCubicSpline() {
            const pts = [...points].sort((a, b) => a.x - b.x);
            const n = pts.length;
            const h = [];
            for (let i = 0; i < n - 1; i++) h.push(pts[i + 1].x - pts[i].x);

            const alpha = [0];
            for (let i = 1; i < n - 1; i++) {
                alpha.push((3 / h[i]) * (pts[i + 1].y - pts[i].y) - (3 / h[i - 1]) * (pts[i].y - pts[i - 1].y));
            }

            const l = Array(n).fill(0), mu = Array(n).fill(0), z = Array(n).fill(0);
            l[0] = 1;

            for (let i = 1; i < n - 1; i++) {
                l[i] = 2 * (pts[i + 1].x - pts[i - 1].x) - h[i - 1] * mu[i - 1];
                mu[i] = h[i] / l[i];
                z[i] = (alpha[i] - h[i - 1] * z[i - 1]) / l[i];
            }
            l[n - 1] = 1;

            const c = Array(n).fill(0), b = Array(n - 1).fill(0), d = Array(n - 1).fill(0);
            for (let j = n - 2; j >= 0; j--) {
                c[j] = z[j] - mu[j] * c[j + 1];
                b[j] = (pts[j + 1].y - pts[j].y) / h[j] - h[j] * (c[j + 1] + 2 * c[j]) / 3;
                d[j] = (c[j + 1] - c[j]) / (3 * h[j]);
            }

            const splineFn = (x) => {
                if (x <= pts[0].x) return pts[0].y;
                if (x >= pts[n - 1].x) return pts[n - 1].y;
                let i = 0;
                for (let k = 0; k < n - 1; k++) {
                    if (x >= pts[k].x && x <= pts[k + 1].x) { i = k; break; }
                }
                const dx = x - pts[i].x;
                return pts[i].y + b[i] * dx + c[i] * dx * dx + d[i] * dx * dx * dx;
            };

            return { text: `Splines Cúbicos construidos (${n - 1} tramos)`, fn: splineFn, pts };
        }

        // 3. Roots Methods
        function calcRoots() {
            const expr = document.getElementById('fxInput').value;
            const tol = parseFloat(document.getElementById('tolInput').value) || 0.0001;
            const maxIter = parseInt(document.getElementById('maxIterInput').value) || 50;
            const x0 = parseFloat(document.getElementById('x0Input').value) || 0;
            const x1 = parseFloat(document.getElementById('x1Input').value) || 0;
            const method = methodSelect.value;

            const f = (x) => math.evaluate(expr, { x });

            let root = null, iter = 0, err = 0;

            if (method === 'bisection') {
                let a = x0, b = x1;
                if (f(a) * f(b) > 0) return { error: 'f(a) y f(b) deben tener signos opuestos' };

                let cPrev = a;
                for (let i = 1; i <= maxIter; i++) {
                    const c = (a + b) / 2;
                    err = Math.abs(c - cPrev);
                    if (Math.abs(f(c)) < tol || err < tol) { root = c; iter = i; break; }
                    if (f(a) * f(c) < 0) b = c; else a = c;
                    cPrev = c;
                    iter = i;
                    root = c;
                }
            } else if (method === 'newton_raphson') {
                const dfExpr = math.derivative(expr, 'x');
                const df = (x) => dfExpr.evaluate({ x });
                let currX = x0;

                for (let i = 1; i <= maxIter; i++) {
                    const nextX = currX - (f(currX) / df(currX));
                    err = Math.abs(nextX - currX);
                    if (err < tol) { root = nextX; iter = i; break; }
                    currX = nextX;
                    iter = i;
                    root = currX;
                }
            } else if (method === 'secant') {
                let p0 = x0, p1 = x1;
                for (let i = 1; i <= maxIter; i++) {
                    const p2 = p1 - (f(p1) * (p1 - p0)) / (f(p1) - f(p0));
                    err = Math.abs(p2 - p1);
                    if (err < tol) { root = p2; iter = i; break; }
                    p0 = p1; p1 = p2;
                    iter = i;
                    root = p2;
                }
            }

            return {
                text: `Raíz Aproximada: ${root.toFixed(6)} | Iteraciones: ${iter} | Error: ${err.toExponential(3)}`,
                fn: f,
                root
            };
        }

        // Plotting
        function drawChart(fn, pointsArr = [], rootVal = null) {
            const ctx = document.getElementById('chartCanvas').getContext('2d');
            if (chartInstance) chartInstance.destroy();

            let minX = -5, maxX = 5;
            if (pointsArr.length > 0) {
                const xs = pointsArr.map(p => p.x);
                minX = Math.min(...xs) - 1;
                maxX = Math.max(...xs) + 1;
            } else if (rootVal !== null) {
                minX = rootVal - 3;
                maxX = rootVal + 3;
            }

            const curveData = [];
            const step = (maxX - minX) / 100;
            for (let x = minX; x <= maxX; x += step) {
                try {
                    curveData.push({ x: x, y: fn(x) });
                } catch(e){}
            }

            const datasets = [{
                label: 'f(x)',
                data: curveData,
                borderColor: '#2563eb',
                borderWidth: 2,
                pointRadius: 0,
                showLine: true
            }];

            if (pointsArr.length > 0) {
                datasets.push({
                    label: 'Puntos',
                    data: pointsArr,
                    backgroundColor: '#ef4444',
                    pointRadius: 5,
                    showLine: false
                });
            }

            if (rootVal !== null) {
                datasets.push({
                    label: 'Raíz',
                    data: [{ x: rootVal, y: fn(rootVal) }],
                    backgroundColor: '#10b981',
                    pointRadius: 7,
                    showLine: false
                });
            }

            chartInstance = new Chart(ctx, {
                type: 'scatter',
                data: { datasets },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        x: { type: 'linear', position: 'bottom' }
                    }
                }
            });
        }

        // Execution
        calcBtn.addEventListener('click', () => {
            const cat = categorySelect.value;
            const method = methodSelect.value;

            try {
                if (cat === 'interpolation') {
                    const res = method === 'newton' ? calcNewtonInterpolation() : calcCubicSpline();
                    resultOutput.textContent = res.text;
                    drawChart(res.fn, res.pts);
                } else {
                    const res = calcRoots();
                    if (res.error) {
                        resultOutput.textContent = res.error;
                    } else {
                        resultOutput.textContent = res.text;
                        drawChart(res.fn, [], res.root);
                    }
                }
            } catch (err) {
                resultOutput.textContent = 'Error al calcular. Revisa los datos de entrada.';
            }
        });

        // Initialize
        updateMethods();
        renderTable();
    </script>
</body>
</html>
