<!DOCTYPE html>
<html lang="es" class="h-full bg-[#09090b]">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vape Crew — Panel Operativo</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        brand: {
                            dark: '#09090b',
                            card: '#18181b',
                            border: '#27272a',
                            accent: '#10b981'
                        }
                    }
                }
            }
        }
    </script>
    <script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        scrollbar-width: thin;
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: #09090b; }
        ::-webkit-scrollbar-thumb { background: #27272a; border-radius: 3px; }
    </style>
</head>
<body class="h-full text-zinc-100 font-sans antialiased" x-data="dashboard()" x-init="initApp()">

    <div class="min-h-full flex flex-col">
        <header class="border-b border-zinc-800 bg-[#09090b]/80 backdrop-blur-md sticky top-0 z-40">
            <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
                <div class="flex items-center gap-3">
                    <div class="h-8 w-8 rounded-lg bg-emerald-500/10 border border-emerald-500/30 flex items-center justify-center text-emerald-400 font-bold text-sm">VC</div>
                    <span class="font-semibold tracking-tight text-zinc-200 text-lg">Vape Crew <span class="text-zinc-500 font-normal text-xs ml-1">Admin OS</span></span>
                </div>
                
                <div class="flex items-center gap-2 max-w-xs sm:max-w-md w-full justify-end">
                    <div class="relative w-full max-w-xs">
                        <input type="password" 
                               x-model="sheetUrl" 
                               @change="saveUrl()"
                               placeholder="URL de Google Apps Script..." 
                               class="w-full bg-[#18181b] border border-zinc-800 rounded-lg px-3 py-1.5 text-xs text-zinc-300 placeholder-zinc-600 focus:outline-none focus:border-emerald-500 transition-colors">
                    </div>
                    <button @click="fetchData()" class="p-1.5 bg-zinc-800 hover:bg-zinc-700 border border-zinc-700 rounded-lg text-zinc-300 transition-colors" title="Sincronizar ahora">
                        <i data-lucide="refresh-cw" class="w-4 h-4" :class="loading ? 'animate-spin text-emerald-400' : ''"></i>
                    </button>
                </div>
            </div>
        </header>

        <main class="flex-1 mx-auto max-w-7xl px-4 sm:px-6 lg:px-8 py-8 w-full">
            
            <template x-if="error">
                <div class="mb-6 p-4 bg-rose-500/10 border border-rose-500/20 rounded-xl text-rose-400 text-sm flex items-center gap-3">
                    <i data-lucide="alert-circle" class="w-5 h-5 shrink-0"></i>
                    <span x-text="error"></span>
                </div>
            </template>

            <div class="grid grid-cols-2 lg:grid-cols-4 gap-4 mb-8">
                <div class="bg-[#18181b] border border-zinc-800/80 rounded-xl p-5 relative overflow-hidden">
                    <div class="text-xs font-medium text-zinc-500 uppercase tracking-wider mb-2">Valor Inventario (Costo)</div>
                    <div class="text-2xl font-semibold tracking-tight text-zinc-100" x-text="formatCurrency(metrics.inventoryValue)">$0</div>
                    <div class="text-xs text-zinc-400 mt-2 flex items-center gap-1">
                        <span class="text-emerald-400 font-medium" x-text="products.length">0</span> productos listados
                    </div>
                </div>
                <div class="bg-[#18181b] border border-zinc-800/80 rounded-xl p-5 relative overflow-hidden">
                    <div class="text-xs font-medium text-zinc-500 uppercase tracking-wider mb-2">Ganancia Potencial</div>
                    <div class="text-2xl font-semibold tracking-tight text-emerald-400" x-text="formatCurrency(metrics.potentialProfit)">$0</div>
                    <div class="text-xs text-zinc-500 mt-2">Retorno estimado total</div>
                </div>
                <div class="bg-[#18181b] border border-zinc-800/80 rounded-xl p-5 relative overflow-hidden">
                    <div class="text-xs font-medium text-zinc-500 uppercase tracking-wider mb-2">Margen Promedio</div>
                    <div class="text-2xl font-semibold tracking-tight text-zinc-100" x-text="metrics.avgMargin.toFixed(1) + '%'">0%</div>
                    <div class="text-xs text-zinc-400 mt-2 flex items-center gap-1">
                        Rentabilidad global del catálogo
                    </div>
                </div>
                <div class="bg-[#18181b] border border-zinc-800/80 rounded-xl p-5 relative overflow-hidden">
                    <div class="text-xs font-medium text-zinc-500 uppercase tracking-wider mb-2">Estado del Stock</div>
                    <div class="text-2xl font-semibold tracking-tight text-zinc-100" x-text="metrics.totalStock + ' u.'">0 u.</div>
                    <div class="text-xs mt-2 flex gap-2">
                        <span class="text-rose-400" x-text="metrics.outOfStock + ' Agotados'"></span>
                        <span class="text-amber-400" x-text="metrics.lowStock + ' Bajos'"></span>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 mb-8">
                <div class="bg-[#18181b] border border-zinc-800 rounded-xl p-5 lg:col-span-2">
                    <h3 class="text-sm font-medium text-zinc-300 mb-4">Distribución Financiera por Categoría</h3>
                    <div class="h-64 relative">
                        <canvas id="categoryChart"></canvas>
                    </div>
                </div>
                <div class="bg-[#18181b] border border-zinc-800 rounded-xl p-5">
                    <h3 class="text-sm font-medium text-zinc-300 mb-4">Salud del Catálogo</h3>
                    <div class="h-64 flex items-center justify-center relative">
                        <canvas id="stockPieChart"></canvas>
                    </div>
                </div>
            </div>

            <div class="bg-[#18181b] border border-zinc-800 rounded-xl overflow-hidden">
                <div class="p-5 border-b border-zinc-800 bg-[#1c1c1f]/50 flex flex-col sm:flex-row gap-4 items-center justify-between">
                    <div class="relative w-full sm:w-72">
                        <span class="absolute inset-y-0 left-0 flex items-center pl-3 pointer-events-none text-zinc-500">
                            <i data-lucide="search" class="w-4 h-4"></i>
                        </span>
                        <input type="text" x-model="searchQuery" placeholder="Buscar por producto o sabor..." class="w-full bg-[#09090b] border border-zinc-800 rounded-lg pl-9 pr-4 py-2 text-sm text-zinc-200 placeholder-zinc-500 focus:outline-none focus:border-zinc-700">
                    </div>
                    
                    <div class="flex flex-wrap w-full sm:w-auto gap-2 justify-end">
                        <select x-model="categoryFilter" class="bg-[#09090b] border border-zinc-800 rounded-lg px-3 py-2 text-sm text-zinc-300 focus:outline-none focus:border-zinc-700">
                            <option value="">Todas las Categorías</option>
                            <template x-for="cat in categories" :key="cat">
                                <option :value="cat" x-text="cat"></option>
                            </template>
                        </select>

                        <select x-model="statusFilter" class="bg-[#09090b] border border-zinc-800 rounded-lg px-3 py-2 text-sm text-zinc-300 focus:outline-none focus:border-zinc-700">
                            <option value="">Todos los Estados</option>
                            <option value="SIN STOCK">Sin Stock</option>
                            <option value="STOCK BAJO">Stock Bajo</option>
                            <option value="STOCK OPTIMO">Stock Óptimo</option>
                        </select>
                    </div>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="border-b border-zinc-800 text-zinc-400 text-xs font-medium uppercase bg-[#1c1c1f]/20">
                                <th class="py-3.5 px-4 cursor-pointer hover:text-white" @click="sort('id')">ID</th>
                                <th class="py-3.5 px-4 cursor-pointer hover:text-white" @click="sort('name')">Producto / Sabor</th>
                                <th class="py-3.5 px-4 cursor-pointer hover:text-white" @click="sort('category')">Categoría</th>
                                <th class="py-3.5 px-4 text-right cursor-pointer hover:text-white" @click="sort('cost')">Costo</th>
                                <th class="py-3.5 px-4 text-right cursor-pointer hover:text-white" @click="sort('salePrice')">P. Venta</th>
                                <th class="py-3.5 px-4 text-center cursor-pointer hover:text-white" @click="sort('stock')">Stock</th>
                                <th class="py-3.5 px-4 text-center">Estado</th>
                                <th class="py-3.5 px-4 text-right">Margen</th>
                            </tr>
                        </thead>
                        <tbody class="divide-y divide-zinc-800/50 text-sm">
                            <template x-for="p in filteredProducts()" :key="p.id">
                                <tr class="hover:bg-zinc-800/30 transition-colors">
                                    <td class="py-3.5 px-4 text-zinc-500 font-mono text-xs" x-text="p.id"></td>
                                    <td class="py-3.5 px-4">
                                        <div class="font-medium text-zinc-200" x-text="p.name"></div>
                                        <div class="text-xs text-zinc-400" x-text="p.flavor"></div>
                                    </td>
                                    <td class="py-3.5 px-4 text-zinc-400 text-xs" x-text="p.category"></td>
                                    <td class="py-3.5 px-4 text-right font-mono text-zinc-400" x-text="formatCurrency(p.cost)"></td>
                                    <td class="py-3.5 px-4 text-right font-mono text-zinc-200" x-text="formatCurrency(p.salePrice)"></td>
                                    <td class="py-3.5 px-4 text-center font-mono font-medium" :class="p.stock === 0 ? 'text-rose-400' : p.stock <= 2 ? 'text-amber-400' : 'text-zinc-300'" x-text="p.stock"></td>
                                    <td class="py-3.5 px-4 text-center">
                                        <span class="inline-flex items-center gap-1.5 px-2 py-0.5 rounded-full text-xs font-medium"
                                              :class="p.stock === 0 ? 'bg-rose-500/10 text-rose-400' : p.stock <= 2 ? 'bg-amber-500/10 text-amber-400' : 'bg-emerald-500/10 text-emerald-400'">
                                            <span class="w-1.5 h-1.5 rounded-full" :class="p.stock === 0 ? 'bg-rose-400' : p.stock <= 2 ? 'bg-amber-400' : 'bg-emerald-400'"></span>
                                            <span x-text="p.stock === 0 ? 'Sin Stock' : p.stock <= 2 ? 'Bajo' : 'Óptimo'"></span>
                                        </span>
                                    </td>
                                    <td class="py-3.5 px-4 text-right font-mono text-emerald-400 font-medium" x-text="p.salePrice > 0 ? ((p.salePrice - p.cost) / p.salePrice * 100).toFixed(0) + '%' : '0%'"></td>
                                </tr>
                            </template>
                        </tbody>
                    </table>
                </div>
            </div>
        </main>
    </div>

    <script>
        function dashboard() {
            return {
                // URL integrada directamente por defecto
                sheetUrl: 'https://script.google.com/macros/s/AKfycbwiJQnEG8DaPdTtXeP6vKKKB813pqGNul6LLXQnf6_-D1j4ESTPfcCm4p4Xf87fASn7aA/exec',
                products: [],
                categories: [],
                loading: false,
                error: null,
                searchQuery: '',
                categoryFilter: '',
                statusFilter: '',
                sortKey: 'id',
                sortAsc: true,
                charts: {},
                metrics: { inventoryValue: 0, potentialProfit: 0, avgMargin: 0, totalStock: 0, outOfStock: 0, lowStock: 0 },

                initApp() {
                    // Si el usuario guardó una URL personalizada antes en LocalStorage la respetamos, si no usamos la tuya
                    this.sheetUrl = localStorage.getItem('vc_sheet_url') || this.sheetUrl;
                    this.fetchData();
                    setTimeout(() => lucide.createIcons(), 100);
                },

                saveUrl() {
                    localStorage.setItem('vc_sheet_url', this.sheetUrl);
                    this.fetchData();
                },

                async fetchData() {
                    if (!this.sheetUrl) return;
                    this.loading = true;
                    this.error = null;
                    try {
                        const response = await fetch(this.sheetUrl);
                        if (!response.ok) throw new Error("Error de red al consultar la API.");
                        const data = await response.json();
                        
                        if (data.error) throw new Error(data.error);
                        
                        this.products = Array.isArray(data) ? data : data.productos || [];
                        this.extractCategories();
                        this.calculateMetrics();
                        this.renderCharts();
                    } catch (err) {
                        this.error = `Error de sincronización: ${err.message}. Asegúrate de que el Apps Script esté implementado como "Cualquiera" (Anyone) con acceso de lectura.`;
                    } finally {
                        this.loading = false;
                        setTimeout(() => lucide.createIcons(), 50);
                    }
                },

                extractCategories() {
                    const cats = this.products.map(p => p.category).filter(c => c && c !== "");
                    this.categories = [...new Set(cats)];
                },

                calculateMetrics() {
                    let totalCost = 0, totalProfit = 0, totalMarginSum = 0, stockTotal = 0, outStock = 0, lowStock = 0;
                    
                    this.products.forEach(p => {
                        totalCost += (p.stock * p.cost);
                        totalProfit += (p.stock * (p.salePrice - p.cost));
                        stockTotal += p.stock;
                        
                        if (p.stock === 0) outStock++;
                        else if (p.stock <= 2) lowStock++;
                        
                        const margin = p.salePrice > 0 ? ((p.salePrice - p.cost) / p.salePrice) * 100 : 0;
                        totalMarginSum += margin;
                    });

                    this.metrics = {
                        inventoryValue: totalCost,
                        potentialProfit: totalProfit,
                        avgMargin: this.products.length > 0 ? (totalMarginSum / this.products.length) : 0,
                        totalStock: stockTotal,
                        outOfStock: outStock,
                        lowStock: lowStock
                    };
                },

                filteredProducts() {
                    return this.products.filter(p => {
                        const matchesSearch = p.name.toLowerCase().includes(this.searchQuery.toLowerCase()) || 
                                              p.flavor.toLowerCase().includes(this.searchQuery.toLowerCase());
                        const matchesCat = this.categoryFilter === '' || p.category === this.categoryFilter;
                        
                        let matchesStatus = true;
                        if (this.statusFilter === 'SIN STOCK') matchesStatus = p.stock === 0;
                        else if (this.statusFilter === 'STOCK BAJO') matchesStatus = p.stock > 0 && p.stock <= 2;
                        else if (this.statusFilter === 'STOCK OPTIMO') matchesStatus = p.stock > 2;

                        return matchesSearch && matchesCat && matchesStatus;
                    }).sort((a, b) => {
                        let valA = a[this.sortKey];
                        let valB = b[this.sortKey];
                        if (typeof valA === 'string') {
                            return this.sortAsc ? valA.localeCompare(valB) : valB.localeCompare(valA);
                        }
                        return this.sortAsc ? valA - valB : valB - valA;
                    });
                },

                sort(key) {
                    if (this.sortKey === key) {
                        this.sortAsc = !this.sortAsc;
                    } else {
                        this.sortKey = key;
                        this.sortAsc = true;
                    }
                },

                formatCurrency(value) {
                    return new Intl.NumberFormat('es-AR', { style: 'currency', currency: 'ARS', maximumFractionDigits: 0 }).format(value);
                },

                renderCharts() {
                    const catMap = {};
                    this.products.forEach(p => {
                        if (!catMap[p.category]) catMap[p.category] = { cost: 0, profit: 0 };
                        catMap[p.category].cost += (p.stock * p.cost);
                        catMap[p.category].profit += (p.stock * (p.salePrice - p.cost));
                    });

                    const labels = Object.keys(catMap);
                    const costData = labels.map(l => catMap[l].cost);
                    const profitData = labels.map(l => catMap[l].profit);

                    if (this.charts.category) this.charts.category.destroy();
                    if (this.charts.pie) this.charts.pie.destroy();

                    // 1. Gráfico de Barras (Categorías)
                    const ctxBar = document.getElementById('categoryChart').getContext('2d');
                    this.charts.category = new Chart(ctxBar, {
                        type: 'bar',
                        data: {
                            labels: labels,
                            datasets: [
                                { label: 'Capital Invertido', data: costData, backgroundColor: '#3f3f46', borderRadius: 4 },
                                { label: 'Ganancia Potencial', data: profitData, backgroundColor: '#10b981', borderRadius: 4 }
                            ]
                        },
                        options: {
                            responsive: true,
                            maintainAspectRatio: false,
                            plugins: { legend: { labels: { color: '#a1a1aa' } } },
                            scales: {
                                x: { grid: { display: false }, ticks: { color: '#71717a' } },
                                y: { grid: { color: '#27272a' }, ticks: { color: '#71717a' } }
                            }
                        }
                    });

                    // 2. Gráfico de Torta (Salud de Stock)
                    const ctxPie = document.getElementById('stockPieChart').getContext('2d');
                    this.charts.pie = new Chart(ctxPie, {
                        type: 'doughnut',
                        data: {
                            labels: ['Óptimo', 'Bajo', 'Agotado'],
                            datasets: [{
                                data: [
                                    this.products.filter(p => p.stock > 2).length,
                                    this.metrics.lowStock,
                                    this.metrics.outOfStock
                                ],
                                backgroundColor: ['#10b981', '#f59e0b', '#f43f5e'],
                                borderWidth: 0
                            }]
                        },
                        options: {
                            responsive: true,
                            maintainAspectRatio: false,
                            plugins: { legend: { position: 'bottom', labels: { color: '#a1a1aa', padding: 20 } } },
                            cutout: '75%'
                        }
                    });
                }
            }
        }
    </script>
</body>
</html>
