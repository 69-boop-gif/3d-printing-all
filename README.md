<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D-Printing Hub - Shop & Admin Portal</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Chart.js for Admin Analytics -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Inter Font -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
    
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        brand: {
                            50: '#f0f9ff',
                            100: '#e0f2fe',
                            500: '#0284c7',
                            600: '#0369a1',
                            700: '#0369a1',
                            900: '#0c4a6e',
                        },
                        accent: {
                            500: '#8b5cf6',
                            600: '#7c3aed',
                        }
                    },
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    }
                }
            }
        }
    </script>
    <style>
        body { font-family: 'Inter', sans-serif; }
        .glass { background: rgba(255, 255, 255, 0.85); backdrop-filter: blur(12px); }
        .dark .glass { background: rgba(15, 23, 42, 0.85); backdrop-filter: blur(12px); }
        .custom-scrollbar::-webkit-scrollbar { width: 6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: transparent; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 9999px; }
        .dark .custom-scrollbar::-webkit-scrollbar-thumb { background: #334155; }
    </style>
</head>
<body class="bg-slate-50 dark:bg-slate-900 text-slate-800 dark:text-slate-100 min-h-screen flex flex-col transition-colors duration-200">

    <!-- TOP NAVBAR -->
    <header class="sticky top-0 z-40 w-full glass border-b border-slate-200 dark:border-slate-800 shadow-sm">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center justify-between h-16">
                <!-- Logo -->
                <div class="flex items-center space-x-3 cursor-pointer" onclick="switchView('shop')">
                    <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-brand-500 to-accent-600 flex items-center justify-center text-white shadow-lg shadow-brand-500/30">
                        <i class="fa-solid font-bold fa-cube text-xl"></i>
                    </div>
                    <div>
                        <span class="text-xl font-extrabold bg-clip-text text-transparent bg-gradient-to-r from-brand-600 to-accent-600">3D-PRINTING.ALL</span>
                        <span class="block text-[10px] text-slate-500 font-medium tracking-widest uppercase">Custom Additive Studio</span>
                    </div>
                </div>

                <!-- VIEW MODE SWITCHER (Shop vs. Verkäufer Admin) -->
                <div class="hidden md:flex items-center bg-slate-200/80 dark:bg-slate-800 p-1 rounded-xl border border-slate-300/50 dark:border-slate-700">
                    <button id="nav-btn-shop" onclick="switchView('shop')" class="px-4 py-1.5 rounded-lg text-xs font-semibold transition-all flex items-center space-x-2 bg-white dark:bg-slate-700 text-slate-900 dark:text-white shadow-sm">
                        <i class="fa-solid fa-store text-brand-500"></i>
                        <span>Kunden-Shop</span>
                    </button>
                    <button id="nav-btn-admin" onclick="switchView('admin')" class="px-4 py-1.5 rounded-lg text-xs font-semibold transition-all flex items-center space-x-2 text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white">
                        <i class="fa-solid fa-chart-line text-amber-500"></i>
                        <span>Verkäufer Dashboard</span>
                        <span id="badge-pending-orders" class="bg-amber-500 text-white text-[10px] font-bold px-1.5 py-0.5 rounded-full ml-1">2</span>
                    </button>
                </div>

                <!-- Right Header Actions -->
                <div class="flex items-center space-x-3">
                    <!-- User Auth Button / Indicator -->
                    <button onclick="openAuthModal()" id="user-auth-btn" class="flex items-center space-x-2 text-xs font-medium bg-slate-100 dark:bg-slate-800 hover:bg-slate-200 dark:hover:bg-slate-700 px-3 py-2 rounded-xl transition">
                        <i class="fa-solid fa-user-circle text-lg text-brand-500"></i>
                        <span id="user-display-name" class="hidden sm:inline">Anmelden</span>
                    </button>

                    <!-- Cart Toggle Button -->
                    <button onclick="toggleCartDrawer()" class="relative p-2.5 bg-brand-500 hover:bg-brand-600 text-white rounded-xl shadow-md shadow-brand-500/20 transition flex items-center justify-center">
                        <i class="fa-solid fa-cart-shopping"></i>
                        <span id="cart-badge-count" class="absolute -top-1.5 -right-1.5 bg-red-500 text-white text-[10px] font-bold w-5 h-5 rounded-full flex items-center justify-center border-2 border-white dark:border-slate-900">0</span>
                    </button>

                    <!-- Mobile Menu Switcher Toggle -->
                    <button onclick="toggleMobileViewMenu()" class="md:hidden p-2 text-slate-600 dark:text-slate-300">
                        <i class="fa-solid fa-bars text-lg"></i>
                    </button>
                </div>
            </div>
        </div>

        <!-- Mobile Navigation Switcher Drawer -->
        <div id="mobile-view-menu" class="hidden md:hidden border-t border-slate-200 dark:border-slate-800 bg-slate-100 dark:bg-slate-800/95 px-4 py-3 flex justify-around">
            <button onclick="switchView('shop'); toggleMobileViewMenu();" class="flex-1 py-2 text-center text-xs font-bold text-brand-600 dark:text-brand-400">
                <i class="fa-solid fa-store mr-1"></i> Shop
            </button>
            <button onclick="switchView('admin'); toggleMobileViewMenu();" class="flex-1 py-2 text-center text-xs font-bold text-amber-600 dark:text-amber-400">
                <i class="fa-solid fa-chart-line mr-1"></i> Admin Dashboard
            </button>
        </div>
    </header>

    <!-- MAIN CONTENT AREA -->
    <main class="flex-grow max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6">

        <!-- ========================================== -->
        <!-- VIEW 1: CUSTOMER SHOP VIEW                 -->
        <!-- ========================================== -->
        <section id="view-shop" class="space-y-8">
            
            <!-- Creator Hero Banner -->
            <div class="relative overflow-hidden rounded-3xl bg-gradient-to-r from-slate-900 via-indigo-950 to-slate-900 text-white p-6 sm:p-10 shadow-2xl border border-slate-800">
                <div class="absolute right-0 top-0 opacity-10 pointer-events-none transform translate-x-10 -translate-y-10">
                    <i class="fa-solid fa-cube text-[300px]"></i>
                </div>
                <div class="relative z-10 max-w-2xl">
                    <span class="inline-block px-3 py-1 bg-brand-500/20 text-brand-300 border border-brand-500/30 rounded-full text-xs font-semibold uppercase tracking-wider mb-3">
                        <i class="fa-solid fa-print mr-1"></i> High-Precision 3D Print Studio
                    </span>
                    <h1 class="text-3xl sm:text-5xl font-extrabold tracking-tight leading-tight">
                        Einzigartige 3D-Drucke direkt vom Ersteller
                    </h1>
                    <p class="mt-3 text-slate-300 text-sm sm:text-base leading-relaxed">
                        Willkommen in meiner Manufaktur! Ich fertige maßgeschneiderte Deko-Objekte, funktionale Ersatzteile, Miniaturmodelle & Cosplay-Accessoires in höchster Präzision (PLA+, PETG, Resin).
                    </p>
                    <div class="mt-6 flex flex-wrap items-center gap-4 text-xs text-slate-300">
                        <div class="flex items-center space-x-1.5 bg-slate-800/80 px-3 py-1.5 rounded-lg border border-slate-700">
                            <i class="fa-solid fa-circle-check text-emerald-400"></i>
                            <span>Bambu Lab X1C & Formlabs Druck</span>
                        </div>
                        <div class="flex items-center space-x-1.5 bg-slate-800/80 px-3 py-1.5 rounded-lg border border-slate-700">
                            <i class="fa-solid fa-truck-fast text-brand-400"></i>
                            <span>Expressversand aus Deutschland</span>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Filter & Search Toolbar -->
            <div class="flex flex-col md:flex-row md:items-center justify-between gap-4 bg-white dark:bg-slate-800 p-4 rounded-2xl shadow-sm border border-slate-200 dark:border-slate-700">
                
                <!-- Search Input -->
                <div class="relative flex-1">
                    <i class="fa-solid fa-magnifying-glass absolute left-3.5 top-1/2 -translate-y-1/2 text-slate-400"></i>
                    <input type="text" id="shop-search-input" onkeyup="filterProducts()" placeholder="3D Modell suchen (z.B. Drache, Vasen, Gadgets)..." class="w-full pl-10 pr-4 py-2.5 rounded-xl bg-slate-100 dark:bg-slate-900 border border-slate-200 dark:border-slate-700 text-sm focus:outline-none focus:ring-2 focus:ring-brand-500">
                </div>

                <!-- Category Filters -->
                <div class="flex items-center space-x-2 overflow-x-auto pb-2 md:pb-0 custom-scrollbar">
                    <button onclick="setCategoryFilter('all')" class="category-btn active border px-3.5 py-2 rounded-xl text-xs font-medium whitespace-nowrap transition bg-brand-500 text-white border-brand-500" data-cat="all">Alle</button>
                    <button onclick="setCategoryFilter('Figuren & Articulated')" class="category-btn border px-3.5 py-2 rounded-xl text-xs font-medium whitespace-nowrap transition bg-slate-100 dark:bg-slate-700 text-slate-600 dark:text-slate-300 border-slate-200 dark:border-slate-600 hover:bg-slate-200" data-cat="Figuren & Articulated">Figuren & Tierwelt</button>
                    <button onclick="setCategoryFilter('Deko & Design')" class="category-btn border px-3.5 py-2 rounded-xl text-xs font-medium whitespace-nowrap transition bg-slate-100 dark:bg-slate-700 text-slate-600 dark:text-slate-300 border-slate-200 dark:border-slate-600 hover:bg-slate-200" data-cat="Deko & Design">Deko & Design</button>
                    <button onclick="setCategoryFilter('Tools & Gadgets')" class="category-btn border px-3.5 py-2 rounded-xl text-xs font-medium whitespace-nowrap transition bg-slate-100 dark:bg-slate-700 text-slate-600 dark:text-slate-300 border-slate-200 dark:border-slate-600 hover:bg-slate-200" data-cat="Tools & Gadgets">Tools & Setup</button>
                </div>
            </div>

            <!-- Product Grid -->
            <div>
                <div class="flex items-center justify-between mb-4">
                    <h2 class="text-lg font-bold">Verfügbare 3D-Drucke</h2>
                    <span id="product-count-label" class="text-xs font-medium text-slate-500">4 Produkte</span>
                </div>

                <div id="product-grid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
                    <!-- Dynamic Product Cards injected via JavaScript -->
                </div>
            </div>
        </section>


        <!-- ========================================== -->
        <!-- VIEW 2: SELLER / ADMIN DASHBOARD           -->
        <!-- ========================================== -->
        <section id="view-admin" class="hidden space-y-8">
            
            <!-- Dashboard Title Header -->
            <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 border-b border-slate-200 dark:border-slate-800 pb-5">
                <div>
                    <h1 class="text-2xl font-bold flex items-center gap-2">
                        <span>Verkäufer Kontrollzentrum</span>
                        <span class="bg-amber-100 dark:bg-amber-900/50 text-amber-700 dark:text-amber-400 text-xs px-2.5 py-0.5 rounded-full font-semibold">Admin</span>
                    </h1>
                    <p class="text-xs text-slate-500 dark:text-slate-400 mt-1">Verwalte deine 3D-Drucke, eingehende Bestellungen, Versandadressen und Rabattaktionen.</p>
                </div>
                <div class="flex items-center space-x-3">
                    <button onclick="openAddProductModal()" class="px-4 py-2 bg-emerald-600 hover:bg-emerald-700 text-white rounded-xl text-xs font-bold shadow-md shadow-emerald-600/20 transition flex items-center gap-2">
                        <i class="fa-solid fa-plus"></i>
                        <span>Neuen 3D-Druck hochladen</span>
                    </button>
                </div>
            </div>

            <!-- Analytics KPI Stat Cards -->
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="p-5 bg-white dark:bg-slate-800 rounded-2xl border border-slate-200 dark:border-slate-700 shadow-sm">
                    <div class="flex items-center justify-between text-slate-500 dark:text-slate-400 mb-2">
                        <span class="text-xs font-semibold uppercase">Gesamteinnahmen</span>
                        <div class="p-2 bg-emerald-100 dark:bg-emerald-900/30 text-emerald-600 rounded-lg"><i class="fa-solid fa-euro-sign"></i></div>
                    </div>
                    <div class="text-2xl font-extrabold" id="kpi-total-revenue">€0,00</div>
                    <span class="text-[11px] text-emerald-600 font-medium flex items-center gap-1 mt-1">
                        <i class="fa-solid fa-arrow-trend-up"></i> +24% im Vergleich zum Vormonat
                    </span>
                </div>

                <div class="p-5 bg-white dark:bg-slate-800 rounded-2xl border border-slate-200 dark:border-slate-700 shadow-sm">
                    <div class="flex items-center justify-between text-slate-500 dark:text-slate-400 mb-2">
                        <span class="text-xs font-semibold uppercase">Verkäufe</span>
                        <div class="p-2 bg-brand-100 dark:bg-brand-900/30 text-brand-600 rounded-lg"><i class="fa-solid fa-box text-sm"></i></div>
                    </div>
                    <div class="text-2xl font-extrabold" id="kpi-total-orders">0</div>
                    <span class="text-[11px] text-slate-500 mt-1 block">Abgewickelte Bestellungen</span>
                </div>

                <div class="p-5 bg-white dark:bg-slate-800 rounded-2xl border border-slate-200 dark:border-slate-700 shadow-sm">
                    <div class="flex items-center justify-between text-slate-500 dark:text-slate-400 mb-2">
                        <span class="text-xs font-semibold uppercase">Offener Versand</span>
                        <div class="p-2 bg-amber-100 dark:bg-amber-900/30 text-amber-600 rounded-lg"><i class="fa-solid fa-truck-ramp-box"></i></div>
                    </div>
                    <div class="text-2xl font-extrabold text-amber-600" id="kpi-pending-shipments">0</div>
                    <span class="text-[11px] text-amber-600 font-medium mt-1 block">Bereit zum Druck/Paketieren</span>
                </div>

                <div class="p-5 bg-white dark:bg-slate-800 rounded-2xl border border-slate-200 dark:border-slate-700 shadow-sm">
                    <div class="flex items-center justify-between text-slate-500 dark:text-slate-400 mb-2">
                        <span class="text-xs font-semibold uppercase">Aktive Produkte</span>
                        <div class="p-2 bg-purple-100 dark:bg-purple-900/30 text-purple-600 rounded-lg"><i class="fa-solid fa-cubes"></i></div>
                    </div>
                    <div class="text-2xl font-extrabold" id="kpi-active-products">0</div>
                    <span class="text-[11px] text-slate-500 mt-1 block">In deinem Shop gelistet</span>
                </div>
            </div>

            <!-- Chart Section -->
            <div class="p-6 bg-white dark:bg-slate-800 rounded-2xl border border-slate-200 dark:border-slate-700 shadow-sm">
                <h3 class="text-sm font-bold text-slate-700 dark:text-slate-200 mb-4 flex items-center gap-2">
                    <i class="fa-solid fa-chart-column text-brand-500"></i>
                    Monatliche Umsatzübersicht (€)
                </h3>
                <div class="h-64">
                    <canvas id="salesChart"></canvas>
                </div>
            </div>

            <!-- Admin Tabs Navigation -->
            <div class="border-b border-slate-200 dark:border-slate-700">
                <nav class="flex space-x-6">
                    <button onclick="setAdminTab('orders')" id="tab-btn-orders" class="admin-tab-btn pb-3 text-sm font-bold text-brand-600 border-b-2 border-brand-600 flex items-center gap-2">
                        <i class="fa-solid fa-list-check"></i>
                        <span>Bestellungen & Versandadressen</span>
                    </button>
                    <button onclick="setAdminTab('products')" id="tab-btn-products" class="admin-tab-btn pb-3 text-sm font-medium text-slate-500 hover:text-slate-800 dark:hover:text-slate-200 flex items-center gap-2">
                        <i class="fa-solid fa-boxes-stacked"></i>
                        <span>Produkt-Katalog</span>
                    </button>
                    <button onclick="setAdminTab('coupons')" id="tab-btn-coupons" class="admin-tab-btn pb-3 text-sm font-medium text-slate-500 hover:text-slate-800 dark:hover:text-slate-200 flex items-center gap-2">
                        <i class="fa-solid fa-ticket"></i>
                        <span>Rabattcodes verwalten</span>
                    </button>
                </nav>
            </div>

            <!-- TAB 1: ORDERS & SHIPPING ADDRESSES -->
            <div id="admin-tab-content-orders" class="admin-tab-content space-y-4">
                <div class="bg-white dark:bg-slate-800 rounded-2xl border border-slate-200 dark:border-slate-700 overflow-hidden shadow-sm">
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-xs">
                            <thead class="bg-slate-100 dark:bg-slate-900 text-slate-600 dark:text-slate-400 font-bold uppercase tracking-wider">
                                <tr>
                                    <th class="p-3.5">Bestell-ID</th>
                                    <th class="p-3.5">Kunde & Kontakt</th>
                                    <th class="p-3.5">Artikel</th>
                                    <th class="p-3.5">Lieferadresse (Wohin)</th>
                                    <th class="p-3.5">Betrag</th>
                                    <th class="p-3.5">Status</th>
                                    <th class="p-3.5 text-right">Aktion</th>
                                </tr>
                            </thead>
                            <tbody id="admin-orders-table-body" class="divide-y divide-slate-200 dark:divide-slate-700">
                                <!-- Dynamic orders loaded via JS -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>

            <!-- TAB 2: PRODUCT MANAGEMENT -->
            <div id="admin-tab-content-products" class="admin-tab-content hidden space-y-4">
                <div class="bg-white dark:bg-slate-800 rounded-2xl border border-slate-200 dark:border-slate-700 overflow-hidden shadow-sm p-4">
                    <div class="flex justify-between items-center mb-4">
                        <h3 class="font-bold text-sm">Deine hochgeladenen 3D-Drucke</h3>
                        <button onclick="openAddProductModal()" class="px-3 py-1.5 bg-brand-500 text-white rounded-lg text-xs font-bold">
                            + Produkt hinzufügen
                        </button>
                    </div>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-xs">
                            <thead class="bg-slate-100 dark:bg-slate-900 text-slate-600 dark:text-slate-400 font-bold uppercase">
                                <tr>
                                    <th class="p-3">Bild</th>
                                    <th class="p-3">Titel</th>
                                    <th class="p-3">Kategorie</th>
                                    <th class="p-3">Preis</th>
                                    <th class="p-3">Lagerbestand</th>
                                    <th class="p-3 text-right">Aktionen</th>
                                </tr>
                            </thead>
                            <tbody id="admin-products-table-body" class="divide-y divide-slate-200 dark:divide-slate-700">
                                <!-- Products list injected via JS -->
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>

            <!-- TAB 3: COUPONS MANAGEMENT -->
            <div id="admin-tab-content-coupons" class="admin-tab-content hidden space-y-6">
                <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                    <!-- Create Coupon Form -->
                    <div class="bg-white dark:bg-slate-800 p-5 rounded-2xl border border-slate-200 dark:border-slate-700 shadow-sm space-y-4">
                        <h3 class="font-bold text-sm flex items-center gap-2">
                            <i class="fa-solid fa-plus-circle text-brand-500"></i>
                            Neuen Rabattcode erstellen
                        </h3>
                        <div>
                            <label class="block text-xs font-semibold mb-1">Rabattcode Name</label>
                            <input type="text" id="coupon-input-code" placeholder="z.B. SOMMER10 oder DRUCK20" class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-900 border border-slate-200 dark:border-slate-700 text-xs font-mono uppercase">
                        </div>
                        <div class="grid grid-cols-2 gap-2">
                            <div>
                                <label class="block text-xs font-semibold mb-1">Typ</label>
                                <select id="coupon-input-type" class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-900 border border-slate-200 dark:border-slate-700 text-xs">
                                    <option value="percent">Prozent (%)</option>
                                    <option value="fixed">Festbetrag (€)</option>
                                </select>
                            </div>
                            <div>
                                <label class="block text-xs font-semibold mb-1">Wert</label>
                                <input type="number" id="coupon-input-value" placeholder="10" class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-900 border border-slate-200 dark:border-slate-700 text-xs">
                            </div>
                        </div>
                        <button onclick="saveNewCoupon()" class="w-full py-2.5 bg-brand-500 hover:bg-brand-600 text-white rounded-xl text-xs font-bold shadow-md transition">
                            Code aktivieren & speichern
                        </button>
                    </div>

                    <!-- Active Coupons List -->
                    <div class="md:col-span-2 bg-white dark:bg-slate-800 p-5 rounded-2xl border border-slate-200 dark:border-slate-700 shadow-sm">
                        <h3 class="font-bold text-sm mb-4">Aktive Rabattcodes</h3>
                        <div class="overflow-x-auto">
                            <table class="w-full text-left text-xs">
                                <thead class="bg-slate-100 dark:bg-slate-900 text-slate-600 dark:text-slate-400 font-bold uppercase">
                                    <tr>
                                        <th class="p-3">Code</th>
                                        <th class="p-3">Rabatt</th>
                                        <th class="p-3">Nutzungen</th>
                                        <th class="p-3 text-right">Aktion</th>
                                    </tr>
                                </thead>
                                <tbody id="admin-coupons-table-body" class="divide-y divide-slate-200 dark:divide-slate-700">
                                    <!-- Coupons injected via JS -->
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </div>

        </section>
    </main>

    <!-- ========================================== -->
    <!-- CART SLIDE-IN DRAWER                       -->
    <!-- ========================================== -->
    <div id="cart-drawer-overlay" class="fixed inset-0 bg-slate-900/60 z-50 opacity-0 pointer-events-none transition-opacity duration-300" onclick="toggleCartDrawer()"></div>
    <div id="cart-drawer" class="fixed right-0 top-0 bottom-0 w-full max-w-md bg-white dark:bg-slate-900 z-50 shadow-2xl transform translate-x-full transition-transform duration-300 flex flex-col">
        
        <!-- Drawer Header -->
        <div class="p-4 border-b border-slate-200 dark:border-slate-800 flex items-center justify-between">
            <h3 class="text-base font-bold flex items-center gap-2">
                <i class="fa-solid fa-cart-shopping text-brand-500"></i>
                Dein Warenkorb
            </h3>
            <button onclick="toggleCartDrawer()" class="p-2 text-slate-400 hover:text-slate-600 dark:hover:text-slate-200">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>
        </div>

        <!-- Drawer Body (Items) -->
        <div id="cart-items-container" class="flex-grow overflow-y-auto p-4 space-y-4 custom-scrollbar">
            <!-- Dynamic Cart Items -->
        </div>

        <!-- Drawer Footer (Total & Checkout) -->
        <div class="p-5 border-t border-slate-200 dark:border-slate-800 bg-slate-50 dark:bg-slate-800/50 space-y-3">
            
            <!-- Promo Code Input -->
            <div class="flex gap-2">
                <input type="text" id="cart-coupon-input" placeholder="Rabattcode (z.B. DRUCK10)" class="flex-1 px-3 py-2 bg-white dark:bg-slate-900 border border-slate-200 dark:border-slate-700 rounded-xl text-xs uppercase font-mono">
                <button onclick="applyCouponCode()" class="px-4 py-2 bg-slate-800 dark:bg-slate-700 hover:bg-slate-900 text-white rounded-xl text-xs font-bold transition">Anwenden</button>
            </div>
            <div id="applied-coupon-badge" class="hidden text-xs text-emerald-600 font-semibold flex items-center justify-between bg-emerald-50 dark:bg-emerald-900/30 p-2 rounded-lg">
                <span><i class="fa-solid fa-tag mr-1"></i> Rabatt angewendet!</span>
                <span id="coupon-discount-text">-€0,00</span>
            </div>

            <div class="space-y-1.5 text-xs text-slate-600 dark:text-slate-400">
                <div class="flex justify-between">
                    <span>Zwischensumme:</span>
                    <span id="cart-subtotal" class="font-semibold text-slate-900 dark:text-white">€0,00</span>
                </div>
                <div class="flex justify-between">
                    <span>Versandkosten (DHL/Hermes):</span>
                    <span id="cart-shipping" class="font-semibold text-slate-900 dark:text-white">€4,90</span>
                </div>
                <div class="flex justify-between text-sm font-extrabold text-slate-900 dark:text-white pt-2 border-t border-slate-200 dark:border-slate-700">
                    <span>Gesamtsumme (inkl. MwSt.):</span>
                    <span id="cart-grandtotal" class="text-brand-600 dark:text-brand-400">€0,00</span>
                </div>
            </div>

            <button onclick="proceedToCheckout()" class="w-full py-3 bg-brand-500 hover:bg-brand-600 text-white rounded-xl text-sm font-bold shadow-lg shadow-brand-500/30 transition flex items-center justify-center gap-2">
                <span>Zur Kasse gehen</span>
                <i class="fa-solid fa-arrow-right"></i>
            </button>
        </div>
    </div>


    <!-- ========================================== -->
    <!-- CHECKOUT MODAL FLOW (Multi-Step)           -->
    <!-- ========================================== -->
    <div id="checkout-modal" class="fixed inset-0 z-50 hidden bg-slate-900/70 backdrop-blur-sm flex items-center justify-center p-4">
        <div class="bg-white dark:bg-slate-900 rounded-3xl max-w-2xl w-full p-6 sm:p-8 shadow-2xl border border-slate-200 dark:border-slate-800 relative max-h-[90vh] overflow-y-auto custom-scrollbar">
            
            <button onclick="closeCheckoutModal()" class="absolute top-5 right-5 text-slate-400 hover:text-slate-600 dark:hover:text-slate-200">
                <i class="fa-solid fa-xmark text-xl"></i>
            </button>

            <!-- Checkout Steps Header -->
            <div class="flex items-center justify-between border-b border-slate-200 dark:border-slate-800 pb-4 mb-6">
                <h3 class="text-xl font-bold flex items-center gap-2">
                    <i class="fa-solid fa-shield-halved text-brand-500"></i>
                    Sicherer Checkout
                </h3>
                <div class="flex items-center space-x-2 text-xs font-semibold">
                    <span id="step-dot-1" class="w-6 h-6 rounded-full bg-brand-500 text-white flex items-center justify-center">1</span>
                    <span id="step-dot-2" class="w-6 h-6 rounded-full bg-slate-200 dark:bg-slate-800 text-slate-500 flex items-center justify-center">2</span>
                    <span id="step-dot-3" class="w-6 h-6 rounded-full bg-slate-200 dark:bg-slate-800 text-slate-500 flex items-center justify-center">3</span>
                </div>
            </div>

            <!-- STEP 1: AUTH / CONTACT INFO -->
            <div id="checkout-step-1" class="space-y-4">
                <h4 class="font-bold text-sm">1. Benutzeranmeldung / E-Mail Bestätigung</h4>
                <p class="text-xs text-slate-500">Melde dich an oder bestelle mit Schnell-Login, um den Status deiner 3D-Drucke nachzuverfolgen.</p>

                <!-- Mock OAuth Buttons -->
                <div class="grid grid-cols-2 gap-3">
                    <button onclick="simulateFastAuth('Google')" class="py-2.5 px-4 rounded-xl border border-slate-200 dark:border-slate-700 hover:bg-slate-50 dark:hover:bg-slate-800 text-xs font-semibold flex items-center justify-center gap-2 transition">
                        <i class="fa-brands fa-google text-red-500"></i>
                        <span>Mit Google Konto</span>
                    </button>
                    <button onclick="simulateFastAuth('Apple')" class="py-2.5 px-4 rounded-xl border border-slate-200 dark:border-slate-700 hover:bg-slate-50 dark:hover:bg-slate-800 text-xs font-semibold flex items-center justify-center gap-2 transition">
                        <i class="fa-brands fa-apple"></i>
                        <span>Mit Apple Konto</span>
                    </button>
                </div>

                <div class="relative flex py-2 items-center">
                    <div class="flex-grow border-t border-slate-200 dark:border-slate-800"></div>
                    <span class="flex-shrink mx-4 text-[10px] text-slate-400 uppercase tracking-wider">Oder E-Mail Bestätigungscode</span>
                    <div class="flex-grow border-t border-slate-200 dark:border-slate-800"></div>
                </div>

                <div class="space-y-3">
                    <div>
                        <label class="block text-xs font-medium mb-1">E-Mail-Adresse</label>
                        <div class="flex gap-2">
                            <input type="email" id="checkout-email" placeholder="deine-email@beispiel.de" class="flex-1 p-2.5 rounded-xl bg-slate-100 dark:bg-slate-800 border border-slate-200 dark:border-slate-700 text-xs">
                            <button onclick="sendMockEmailCode()" class="px-3 py-2 bg-brand-500 text-white rounded-xl text-xs font-bold whitespace-nowrap">Code senden</button>
                        </div>
                    </div>
                    <div id="code-input-container" class="hidden">
                        <label class="block text-xs font-medium mb-1 text-emerald-600">Bestätigungscode eingeben (Simulator Code: <strong id="sim-code-display">1234</strong>)</label>
                        <input type="text" id="checkout-code" placeholder="4-stelliger Code" class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-800 border border-slate-200 dark:border-slate-700 text-xs">
                    </div>
                </div>

                <button onclick="goToCheckoutStep2()" class="w-full py-3 bg-brand-500 text-white rounded-xl text-xs font-bold mt-4 shadow-md">
                    Weiter zur Lieferadresse
                </button>
            </div>

            <!-- STEP 2: SHIPPING ADDRESS -->
            <div id="checkout-step-2" class="hidden space-y-4">
                <h4 class="font-bold text-sm">2. Lieferadresse (Wohin soll der 3D-Druck geschickt werden?)</h4>
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-medium mb-1">Vorname *</label>
                        <input type="text" id="ship-firstname" placeholder="Max" class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-800 border border-slate-200 dark:border-slate-700 text-xs">
                    </div>
                    <div>
                        <label class="block text-xs font-medium mb-1">Nachname *</label>
                        <input type="text" id="ship-lastname" placeholder="Mustermann" class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-800 border border-slate-200 dark:border-slate-700 text-xs">
                    </div>
                </div>
                <div>
                    <label class="block text-xs font-medium mb-1">Straße & Hausnummer *</label>
                    <input type="text" id="ship-street" placeholder="Musterstraße 12" class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-800 border border-slate-200 dark:border-slate-700 text-xs">
                </div>
                <div class="grid grid-cols-3 gap-3">
                    <div>
                        <label class="block text-xs font-medium mb-1">PLZ *</label>
                        <input type="text" id="ship-zip" placeholder="10115" class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-800 border border-slate-200 dark:border-slate-700 text-xs">
                    </div>
                    <div class="col-span-2">
                        <label class="block text-xs font-medium mb-1">Stadt *</label>
                        <input type="text" id="ship-city" placeholder="Berlin" class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-800 border border-slate-200 dark:border-slate-700 text-xs">
                    </div>
                </div>

                <div class="flex gap-3 pt-2">
                    <button onclick="goToCheckoutStep1()" class="w-1/3 py-3 border border-slate-300 dark:border-slate-700 rounded-xl text-xs font-bold">Zurück</button>
                    <button onclick="goToCheckoutStep3()" class="w-2/3 py-3 bg-brand-500 text-white rounded-xl text-xs font-bold shadow-md">Weiter zur Zahlung</button>
                </div>
            </div>

            <!-- STEP 3: PAYMENT METHOD -->
            <div id="checkout-step-3" class="hidden space-y-4">
                <h4 class="font-bold text-sm">3. Gutscheinkarte / Zahlungsart wählen</h4>
                <p class="text-xs text-slate-500 dark:text-slate-400">
                    Wähle deine Gutscheinkarte. Du benötigst eine Karte im genauen Wert des Betrags.
                </p>

                <!-- Gift Card Options Radio List -->
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-2.5">
                    <label class="flex items-center p-3 rounded-xl border border-slate-200 dark:border-slate-700 hover:bg-slate-50 dark:hover:bg-slate-800 cursor-pointer transition">
                        <input type="radio" name="payment-method" value="Paysafecard" checked onchange="updatePaymentMethodSelection()" class="text-brand-500">
                        <span class="ml-2.5 text-xs font-bold flex items-center gap-2">
                            <i class="fa-solid fa-shield-halved text-blue-500 text-base"></i> Paysafecard
                        </span>
                    </label>
                    <label class="flex items-center p-3 rounded-xl border border-slate-200 dark:border-slate-700 hover:bg-slate-50 dark:hover:bg-slate-800 cursor-pointer transition">
                        <input type="radio" name="payment-method" value="Wunschgutschein" onchange="updatePaymentMethodSelection()" class="text-brand-500">
                        <span class="ml-2.5 text-xs font-bold flex items-center gap-2">
                            <i class="fa-solid fa-gift text-red-500 text-base"></i> Wunschgutschein
                        </span>
                    </label>
                    <label class="flex items-center p-3 rounded-xl border border-slate-200 dark:border-slate-700 hover:bg-slate-50 dark:hover:bg-slate-800 cursor-pointer transition">
                        <input type="radio" name="payment-method" value="PlayStation" onchange="updatePaymentMethodSelection()" class="text-brand-500">
                        <span class="ml-2.5 text-xs font-bold flex items-center gap-2">
                            <i class="fa-brands fa-playstation text-indigo-600 text-base"></i> PlayStation Store
                        </span>
                    </label>
                    <label class="flex items-center p-3 rounded-xl border border-slate-200 dark:border-slate-700 hover:bg-slate-50 dark:hover:bg-slate-800 cursor-pointer transition">
                        <input type="radio" name="payment-method" value="Google Play" onchange="updatePaymentMethodSelection()" class="text-brand-500">
                        <span class="ml-2.5 text-xs font-bold flex items-center gap-2">
                            <i class="fa-brands fa-google-play text-emerald-500 text-base"></i> Google Play
                        </span>
                    </label>
                    <label class="flex items-center p-3 rounded-xl border border-slate-200 dark:border-slate-700 hover:bg-slate-50 dark:hover:bg-slate-800 cursor-pointer transition">
                        <input type="radio" name="payment-method" value="Amazon" onchange="updatePaymentMethodSelection()" class="text-brand-500">
                        <span class="ml-2.5 text-xs font-bold flex items-center gap-2">
                            <i class="fa-brands fa-amazon text-amber-500 text-base"></i> Amazon Gutschein
                        </span>
                    </label>
                    <label class="flex items-center p-3 rounded-xl border border-slate-200 dark:border-slate-700 hover:bg-slate-50 dark:hover:bg-slate-800 cursor-pointer transition">
                        <input type="radio" name="payment-method" value="PayPal / Kreditkarte" onchange="updatePaymentMethodSelection()" class="text-brand-500">
                        <span class="ml-2.5 text-xs font-bold flex items-center gap-2">
                            <i class="fa-brands fa-paypal text-blue-600 text-base"></i> PayPal / Karte
                        </span>
                    </label>
                </div>

                <!-- Required Card Amount Banner & Code Input -->
                <div class="p-4 bg-amber-50 dark:bg-amber-950/40 border border-amber-200 dark:border-amber-800/60 rounded-2xl space-y-3">
                    <div class="flex items-center justify-between text-xs">
                        <span class="font-bold text-amber-900 dark:text-amber-200 flex items-center gap-1.5">
                            <i class="fa-solid fa-credit-card"></i> Erforderliche Gutscheinkarte:
                        </span>
                        <span id="required-card-value-display" class="font-extrabold text-xs text-amber-700 dark:text-amber-300 bg-amber-200/80 dark:bg-amber-900/60 px-3 py-1.5 rounded-lg border border-amber-300 dark:border-amber-700">
                            Karte im Wert von €0,00
                        </span>
                    </div>

                    <div id="gift-code-input-container" class="space-y-1.5">
                        <label class="block text-xs font-semibold text-slate-700 dark:text-slate-300">
                            Gutscheincode / PIN der Karte eingeben *
                        </label>
                        <input type="text" id="checkout-gift-card-code" placeholder="z.B. 0123-4567-8901-2345" class="w-full p-2.5 rounded-xl bg-white dark:bg-slate-900 border border-slate-300 dark:border-slate-700 text-xs font-mono uppercase tracking-wider focus:ring-2 focus:ring-amber-500">
                        <p class="text-[10px] text-slate-500 dark:text-slate-400">
                            Der Verkäufer prüft den eingegebenen Code und löst die Gutscheinkarte im angegebenen Wert ein.
                        </p>
                    </div>
                </div>

                <div class="p-3 bg-slate-100 dark:bg-slate-800/60 rounded-xl text-xs space-y-1">
                    <div class="flex justify-between font-bold">
                        <span>Endbetrag der Bestellung:</span>
                        <span id="checkout-modal-total-price" class="text-brand-600">€0,00</span>
                    </div>
                </div>

                <div class="flex gap-3 pt-2">
                    <button onclick="goToCheckoutStep2()" class="w-1/3 py-3 border border-slate-300 dark:border-slate-700 rounded-xl text-xs font-bold">Zurück</button>
                    <button onclick="completePaymentAndOrder()" class="w-2/3 py-3 bg-emerald-600 hover:bg-emerald-700 text-white rounded-xl text-xs font-extrabold shadow-lg shadow-emerald-600/30 flex items-center justify-center gap-2">
                        <i class="fa-solid fa-check-circle"></i>
                        <span>Jetzt mit Gutschein bezahlen</span>
                    </button>
                </div>
            </div>

            <!-- STEP 4: ORDER CONFIRMATION / SUCCESS -->
            <div id="checkout-step-4" class="hidden text-center py-6 space-y-4">
                <div class="w-16 h-16 bg-emerald-100 dark:bg-emerald-900/50 text-emerald-500 rounded-full flex items-center justify-center mx-auto text-3xl">
                    <i class="fa-solid fa-circle-check"></i>
                </div>
                <h3 class="text-2xl font-extrabold">Bestellung erfolgreich!</h3>
                <p class="text-xs text-slate-500 max-w-md mx-auto">
                    Vielen Dank! Deine Bestellung wurde an den Ersteller übermittelt. Eine Bestätigungs-E-Mail wurde an <strong id="confirm-email-display">deine E-Mail</strong> geschickt.
                </p>
                <div class="bg-slate-100 dark:bg-slate-800 p-4 rounded-2xl text-xs text-left max-w-md mx-auto space-y-1">
                    <div class="font-bold border-b border-slate-200 dark:border-slate-700 pb-1 mb-2">Bestellübersicht</div>
                    <div><strong>Bestell-ID:</strong> <span id="confirm-order-id" class="font-mono">#3D-99812</span></div>
                    <div><strong>Versand an:</strong> <span id="confirm-address-display">Max Mustermann, Berlin</span></div>
                </div>

                <div class="pt-4 flex justify-center gap-3">
                    <button onclick="closeCheckoutModal(); switchView('admin');" class="px-5 py-2.5 bg-amber-500 text-white rounded-xl text-xs font-bold">
                        <i class="fa-solid fa-chart-line mr-1"></i> Zeige Bestellung im Admin-Dashboard
                    </button>
                    <button onclick="closeCheckoutModal()" class="px-5 py-2.5 bg-slate-200 dark:bg-slate-800 text-slate-800 dark:text-white rounded-xl text-xs font-bold">
                        Weiter shoppen
                    </button>
                </div>
            </div>

        </div>
    </div>


    <!-- ========================================== -->
    <!-- PRODUCT DETAIL MODAL                      -->
    <!-- ========================================== -->
    <div id="product-detail-modal" class="fixed inset-0 z-50 hidden bg-slate-900/70 backdrop-blur-sm flex items-center justify-center p-4">
        <div class="bg-white dark:bg-slate-900 rounded-3xl max-w-2xl w-full p-6 sm:p-8 shadow-2xl border border-slate-200 dark:border-slate-800 relative max-h-[90vh] overflow-y-auto custom-scrollbar">
            <button onclick="closeProductModal()" class="absolute top-5 right-5 text-slate-400 hover:text-slate-600">
                <i class="fa-solid fa-xmark text-xl"></i>
            </button>
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-6" id="product-modal-content">
                <!-- Dynamic Content injected via JS -->
            </div>
        </div>
    </div>

    <!-- ADD PRODUCT MODAL (For Admin Uploads) -->
    <div id="add-product-modal" class="fixed inset-0 z-50 hidden bg-slate-900/70 backdrop-blur-sm flex items-center justify-center p-4">
        <div class="bg-white dark:bg-slate-900 rounded-3xl max-w-md w-full p-6 shadow-2xl border border-slate-200 dark:border-slate-800 relative">
            <button onclick="closeAddProductModal()" class="absolute top-5 right-5 text-slate-400">
                <i class="fa-solid fa-xmark text-xl"></i>
            </button>
            <h3 class="text-lg font-bold mb-4 flex items-center gap-2">
                <i class="fa-solid fa-upload text-brand-500"></i>
                Neuen 3D-Druck hochladen
            </h3>
            <div class="space-y-3 text-xs">
                <div>
                    <label class="block font-semibold mb-1">Titel des Modells</label>
                    <input type="text" id="new-prod-title" placeholder="z.B. Cyberpunk Totenkopf Pflanztopf" class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-800 border border-slate-200 dark:border-slate-700">
                </div>
                <div class="grid grid-cols-2 gap-2">
                    <div>
                        <label class="block font-semibold mb-1">Preis (€)</label>
                        <input type="number" id="new-prod-price" placeholder="24.90" step="0.10" class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-800 border border-slate-200 dark:border-slate-700">
                    </div>
                    <div>
                        <label class="block font-semibold mb-1">Kategorie</label>
                        <select id="new-prod-cat" class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-800 border border-slate-200 dark:border-slate-700">
                            <option value="Figuren & Articulated">Figuren & Articulated</option>
                            <option value="Deko & Design">Deko & Design</option>
                            <option value="Tools & Gadgets">Tools & Gadgets</option>
                        </select>
                    </div>
                </div>
                <div>
                    <label class="block font-semibold mb-1">Beschreibung</label>
                    <textarea id="new-prod-desc" rows="3" placeholder="Filament-Material, Druckzeit, Maße etc..." class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-800 border border-slate-200 dark:border-slate-700"></textarea>
                </div>
                <div>
                    <label class="block font-semibold mb-1">Bild-URL (Optional)</label>
                    <input type="text" id="new-prod-img" placeholder="https://..." class="w-full p-2.5 rounded-xl bg-slate-100 dark:bg-slate-800 border border-slate-200 dark:border-slate-700">
                </div>
                <button onclick="saveNewProduct()" class="w-full py-3 bg-brand-500 text-white rounded-xl font-bold mt-2 shadow-md">
                    Im Shop veröffentlichen
                </button>
            </div>
        </div>
    </div>

    <!-- TOAST NOTIFICATIONS CONTAINER -->
    <div id="toast-container" class="fixed bottom-5 right-5 z-50 space-y-2 pointer-events-none"></div>

    <!-- ========================================== -->
    <!-- JAVASCRIPT APPLICATION LOGIC               -->
    <!-- ========================================== -->
    <script>
        // APP INITIAL STATE & MOCK DATABASE
        let products = [
            {
                id: 1,
                title: "Articulated Crystal Drache 3D",
                category: "Figuren & Articulated",
                price: 29.90,
                stock: 12,
                image: "https://images.unsplash.com/photo-1618005182384-a83a8bd57fbe?w=600&auto=format&fit=crop&q=80",
                description: "Voll beweglicher 3D-gedruckter Kristall-Drache mit geschmeidigen Gliedern. Gedruckt auf Bambu Lab X1C mit Seiden-PLA Regenbogen-Filament.",
                material: "PLA+ Silk",
                dimensions: "45cm Länge"
            },
            {
                id: 2,
                title: "Voronoi Geometrische Deko-Vase",
                category: "Deko & Design",
                price: 19.50,
                stock: 8,
                image: "https://images.unsplash.com/photo-1578749556568-bc2c40e68b61?w=600&auto=format&fit=crop&q=80",
                description: "Moderne Tischvase im mathematischen Voronoi-Waben-Muster. Perfekt für Trockenblumen und moderne Innenarchitektur.",
                material: "PETG Matt Weiß",
                dimensions: "22cm x 12cm"
            },
            {
                id: 3,
                title: "Ergonomische Tastatur-Handballenauflage",
                category: "Tools & Gadgets",
                price: 14.90,
                stock: 25,
                image: "https://images.unsplash.com/photo-1587829741301-dc798b83add3?w=600&auto=format&fit=crop&q=80",
                description: "Handgelenkschoner für mechanische Tastaturen mit Waben-Belüftungsstruktur für ermüdungsfreies Schreiben.",
                material: "TPU Flexibel",
                dimensions: "36cm x 8cm"
            },
            {
                id: 4,
                title: "Cyberpunk Mech Miniatur-Büste",
                category: "Figuren & Articulated",
                price: 34.00,
                stock: 5,
                image: "https://images.unsplash.com/photo-1563089145-599997674d42?w=600&auto=format&fit=crop&q=80",
                description: "Ultra-hochauflösende Resin-Miniatur im Sci-Fi Stil. Grundiert und bereit zum Bemalen oder Ausstellen.",
                material: "High-Detail Resin",
                dimensions: "15cm Höhe"
            }
        ];

        let cart = [];
        let activeCategory = 'all';
        let appliedDiscount = 0; // Euro value or percentage ratio
        let appliedCouponCode = "";

        let coupons = [
            { code: "DRUCK10", type: "percent", value: 10, uses: 14 },
            { code: "WILLKOMMEN5", type: "fixed", value: 5.00, uses: 8 }
        ];

        let orders = [
            {
                id: "#3D-10029",
                customerName: "Laura Schmidt",
                email: "laura.s@example.de",
                items: "Articulated Crystal Drache 3D (x1)",
                total: 34.80,
                paymentMethod: "Paysafecard (Code: 0123-9981-4412-8812)",
                status: "Versendet",
                address: "Mühlenweg 42, 80331 München, Deutschland",
                date: "18.09.2026"
            },
            {
                id: "#3D-10030",
                customerName: "Michael Weber",
                email: "m.weber@web.de",
                items: "Voronoi Deko-Vase (x1), Tastatur-Stütze (x1)",
                total: 39.30,
                paymentMethod: "Wunschgutschein (Code: WG-8812-9901-4122)",
                status: "In Bearbeitung",
                address: "Hauptstraße 15b, 10115 Berlin, Deutschland",
                date: "19.09.2026"
            }
        ];

        let currentUser = null;
        let mockSimulatedEmailCode = "1234";

        // VIEW SWITCHING (Shop <-> Admin)
        function switchView(view) {
            const shopView = document.getElementById('view-shop');
            const adminView = document.getElementById('view-admin');
            const btnShop = document.getElementById('nav-btn-shop');
            const btnAdmin = document.getElementById('nav-btn-admin');

            if (view === 'shop') {
                shopView.classList.remove('hidden');
                adminView.classList.add('hidden');
                btnShop.className = "px-4 py-1.5 rounded-lg text-xs font-semibold transition-all flex items-center space-x-2 bg-white dark:bg-slate-700 text-slate-900 dark:text-white shadow-sm";
                btnAdmin.className = "px-4 py-1.5 rounded-lg text-xs font-semibold transition-all flex items-center space-x-2 text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white";
            } else {
                shopView.classList.add('hidden');
                adminView.classList.remove('hidden');
                btnAdmin.className = "px-4 py-1.5 rounded-lg text-xs font-semibold transition-all flex items-center space-x-2 bg-white dark:bg-slate-700 text-slate-900 dark:text-white shadow-sm";
                btnShop.className = "px-4 py-1.5 rounded-lg text-xs font-semibold transition-all flex items-center space-x-2 text-slate-600 dark:text-slate-400 hover:text-slate-900 dark:hover:text-white";
                renderAdminTables();
                renderChart();
            }
        }

        function setAdminTab(tabName) {
            document.querySelectorAll('.admin-tab-content').forEach(el => el.classList.add('hidden'));
            document.querySelectorAll('.admin-tab-btn').forEach(el => {
                el.classList.remove('text-brand-600', 'border-b-2', 'border-brand-600', 'font-bold');
                el.classList.add('text-slate-500', 'font-medium');
            });

            document.getElementById(`admin-tab-content-${tabName}`).classList.remove('hidden');
            const btn = document.getElementById(`tab-btn-${tabName}`);
            btn.classList.remove('text-slate-500', 'font-medium');
            btn.classList.add('text-brand-600', 'border-b-2', 'border-brand-600', 'font-bold');
        }

        function toggleMobileViewMenu() {
            const menu = document.getElementById('mobile-view-menu');
            menu.classList.toggle('hidden');
        }

        // RENDER PRODUCTS GRID
        function renderProducts() {
            const grid = document.getElementById('product-grid');
            const searchVal = document.getElementById('shop-search-input').value.toLowerCase();
            
            const filtered = products.filter(p => {
                const matchesCat = (activeCategory === 'all' || p.category === activeCategory);
                const matchesSearch = p.title.toLowerCase().includes(searchVal) || p.description.toLowerCase().includes(searchVal);
                return matchesCat && matchesSearch;
            });

            document.getElementById('product-count-label').innerText = `${filtered.length} Produkte`;

            grid.innerHTML = filtered.map(p => `
                <div class="group bg-white dark:bg-slate-800 rounded-3xl overflow-hidden border border-slate-200 dark:border-slate-700/80 shadow-sm hover:shadow-xl transition-all duration-300 flex flex-col">
                    <div class="relative h-48 sm:h-52 overflow-hidden bg-slate-100 dark:bg-slate-900">
                        <img src="${p.image}" alt="${p.title}" class="w-full h-full object-cover group-hover:scale-105 transition-transform duration-500">
                        <span class="absolute top-3 left-3 bg-slate-900/80 backdrop-blur-md text-white text-[10px] font-bold px-2.5 py-1 rounded-full border border-slate-700">
                            ${p.material}
                        </span>
                        <button onclick="openProductModal(${p.id})" class="absolute bottom-3 right-3 bg-white/90 dark:bg-slate-800/90 hover:bg-brand-500 hover:text-white p-2.5 rounded-2xl shadow-md transition">
                            <i class="fa-solid fa-expand text-xs"></i>
                        </button>
                    </div>
                    <div class="p-5 flex-grow flex flex-col justify-between">
                        <div>
                            <span class="text-[10px] font-extrabold uppercase text-brand-600 dark:text-brand-400 tracking-wider">${p.category}</span>
                            <h3 class="font-bold text-sm mt-1 group-hover:text-brand-500 transition line-clamp-1">${p.title}</h3>
                            <p class="text-xs text-slate-500 dark:text-slate-400 mt-1 line-clamp-2">${p.description}</p>
                        </div>
                        <div class="mt-4 pt-3 border-t border-slate-100 dark:border-slate-700/50 flex items-center justify-between">
                            <div>
                                <span class="text-xs text-slate-400 font-medium block">Preis</span>
                                <span class="text-lg font-extrabold text-slate-900 dark:text-white">€${p.price.toFixed(2)}</span>
                            </div>
                            <button onclick="addToCart(${p.id})" class="px-3.5 py-2 bg-slate-900 dark:bg-slate-100 hover:bg-brand-500 text-white dark:text-slate-900 dark:hover:text-white rounded-xl text-xs font-bold transition flex items-center gap-1.5 shadow-sm">
                                <i class="fa-solid fa-cart-plus"></i>
                                <span>In Warenkorb</span>
                            </button>
                        </div>
                    </div>
                </div>
            `).join('');
        }

        function setCategoryFilter(cat) {
            activeCategory = cat;
            document.querySelectorAll('.category-btn').forEach(btn => {
                if (btn.dataset.cat === cat) {
                    btn.className = "category-btn active border px-3.5 py-2 rounded-xl text-xs font-medium whitespace-nowrap transition bg-brand-500 text-white border-brand-500";
                } else {
                    btn.className = "category-btn border px-3.5 py-2 rounded-xl text-xs font-medium whitespace-nowrap transition bg-slate-100 dark:bg-slate-700 text-slate-600 dark:text-slate-300 border-slate-200 dark:border-slate-600 hover:bg-slate-200";
                }
            });
            renderProducts();
        }

        function filterProducts() {
            renderProducts();
        }

        // CART OPERATIONS
        function addToCart(productId) {
            const product = products.find(p => p.id === productId);
            if (!product) return;

            const existing = cart.find(item => item.id === productId);
            if (existing) {
                existing.qty++;
            } else {
                cart.push({ ...product, qty: 1 });
            }

            updateCartUI();
            showToast(`"${product.title}" zum Warenkorb hinzugefügt!`);
        }

        function removeFromCart(productId) {
            cart = cart.filter(item => item.id !== productId);
            updateCartUI();
        }

        function updateCartQty(productId, delta) {
            const item = cart.find(i => i.id === productId);
            if (!item) return;
            item.qty += delta;
            if (item.qty <= 0) {
                removeFromCart(productId);
            } else {
                updateCartUI();
            }
        }

        function updateCartUI() {
            const container = document.getElementById('cart-items-container');
            const totalCount = cart.reduce((acc, i) => acc + i.qty, 0);
            document.getElementById('cart-badge-count').innerText = totalCount;

            if (cart.length === 0) {
                container.innerHTML = `
                    <div class="text-center py-12 text-slate-400">
                        <i class="fa-solid fa-basket-shopping text-4xl mb-3 opacity-40"></i>
                        <p class="text-xs font-medium">Dein Warenkorb ist noch leer.</p>
                    </div>
                `;
            } else {
                container.innerHTML = cart.map(item => `
                    <div class="flex items-center space-x-3 p-3 bg-slate-100 dark:bg-slate-800 rounded-2xl border border-slate-200 dark:border-slate-700">
                        <img src="${item.image}" class="w-14 h-14 object-cover rounded-xl bg-slate-200">
                        <div class="flex-grow">
                            <h4 class="text-xs font-bold line-clamp-1">${item.title}</h4>
                            <span class="text-[11px] text-brand-600 dark:text-brand-400 font-extrabold">€${item.price.toFixed(2)}</span>
                            <div class="flex items-center space-x-2 mt-1">
                                <button onclick="updateCartQty(${item.id}, -1)" class="w-5 h-5 bg-slate-200 dark:bg-slate-700 rounded-md flex items-center justify-center text-xs font-bold">-</button>
                                <span class="text-xs font-bold">${item.qty}</span>
                                <button onclick="updateCartQty(${item.id}, 1)" class="w-5 h-5 bg-slate-200 dark:bg-slate-700 rounded-md flex items-center justify-center text-xs font-bold">+</button>
                            </div>
                        </div>
                        <button onclick="removeFromCart(${item.id})" class="text-slate-400 hover:text-red-500 p-2 text-xs">
                            <i class="fa-solid fa-trash"></i>
                        </button>
                    </div>
                `).join('');
            }

            // Calculations
            const subtotal = cart.reduce((acc, i) => acc + (i.price * i.qty), 0);
            const shipping = cart.length > 0 ? 4.90 : 0.00;
            const grandTotal = Math.max(0, subtotal + shipping - appliedDiscount);

            document.getElementById('cart-subtotal').innerText = `€${subtotal.toFixed(2)}`;
            document.getElementById('cart-shipping').innerText = `€${shipping.toFixed(2)}`;
            document.getElementById('cart-grandtotal').innerText = `€${grandTotal.toFixed(2)}`;
            document.getElementById('checkout-modal-total-price').innerText = `€${grandTotal.toFixed(2)}`;
        }

        function toggleCartDrawer() {
            const drawer = document.getElementById('cart-drawer');
            const overlay = document.getElementById('cart-drawer-overlay');
            drawer.classList.toggle('translate-x-full');
            overlay.classList.toggle('opacity-0');
            overlay.classList.toggle('pointer-events-none');
        }

        // COUPON REDEMPTION
        function applyCouponCode() {
            const codeInput = document.getElementById('cart-coupon-input').value.trim().toUpperCase();
            const coupon = coupons.find(c => c.code === codeInput);

            if (!coupon) {
                showToast("Ungültiger Rabattcode!", "error");
                return;
            }

            const subtotal = cart.reduce((acc, i) => acc + (i.price * i.qty), 0);
            if (coupon.type === 'percent') {
                appliedDiscount = (subtotal * coupon.value) / 100;
            } else {
                appliedDiscount = coupon.value;
            }

            appliedCouponCode = coupon.code;
            document.getElementById('applied-coupon-badge').classList.remove('hidden');
            document.getElementById('coupon-discount-text').innerText = `-€${appliedDiscount.toFixed(2)}`;
            updateCartUI();
            showToast(`Code "${coupon.code}" erfolgreich angewendet!`);
        }

        // CHECKOUT FLOW & MODALS
        function proceedToCheckout() {
            if (cart.length === 0) {
                showToast("Dein Warenkorb ist leer!", "error");
                return;
            }
            toggleCartDrawer();
            document.getElementById('checkout-modal').classList.remove('hidden');
            goToCheckoutStep1();
        }

        function closeCheckoutModal() {
            document.getElementById('checkout-modal').classList.add('hidden');
        }

        function goToCheckoutStep1() {
            document.getElementById('checkout-step-1').classList.remove('hidden');
            document.getElementById('checkout-step-2').classList.add('hidden');
            document.getElementById('checkout-step-3').classList.add('hidden');
            document.getElementById('checkout-step-4').classList.add('hidden');
        }

        function goToCheckoutStep2() {
            document.getElementById('checkout-step-1').classList.add('hidden');
            document.getElementById('checkout-step-2').classList.remove('hidden');
            document.getElementById('checkout-step-3').classList.add('hidden');
        }

        function sendMockEmailCode() {
            const email = document.getElementById('checkout-email').value;
            if (!email.includes('@')) {
                showToast("Bitte eine gültige E-Mail eingeben", "error");
                return;
            }
            document.getElementById('code-input-container').classList.remove('hidden');
            showToast(`Bestätigungscode an ${email} gesendet! Code: ${mockSimulatedEmailCode}`);
        }

        function simulateFastAuth(provider) {
            currentUser = { email: `user.${provider.toLowerCase()}@example.com` };
            document.getElementById('user-display-name').innerText = `Konto: ${provider}`;
            showToast(`Erfolgreich angemeldet mit ${provider}!`);
            goToCheckoutStep2();
        }

        function updatePaymentMethodSelection() {
            const selected = document.querySelector('input[name="payment-method"]:checked');
            const codeContainer = document.getElementById('gift-code-input-container');
            if (!selected) return;

            if (selected.value.includes('PayPal')) {
                codeContainer.classList.add('opacity-50');
                document.getElementById('checkout-gift-card-code').placeholder = "Nicht erforderlich für PayPal";
            } else {
                codeContainer.classList.remove('opacity-50');
                document.getElementById('checkout-gift-card-code').placeholder = `z.B. ${selected.value}-Code eingeben`;
            }
        }

        function goToCheckoutStep3() {
            const firstname = document.getElementById('ship-firstname').value.trim();
            const lastname = document.getElementById('ship-lastname').value.trim();
            const street = document.getElementById('ship-street').value.trim();
            const city = document.getElementById('ship-city').value.trim();

            if (!firstname || !lastname || !street || !city) {
                showToast("Bitte fülle alle Pflichtfelder der Adresse aus!", "error");
                return;
            }

            const subtotal = cart.reduce((acc, i) => acc + (i.price * i.qty), 0);
            const grandTotal = Math.max(0, subtotal + 4.90 - appliedDiscount);

            document.getElementById('required-card-value-display').innerText = `Karte im Wert von €${grandTotal.toFixed(2)}`;
            document.getElementById('checkout-modal-total-price').innerText = `€${grandTotal.toFixed(2)}`;

            document.getElementById('checkout-step-2').classList.add('hidden');
            document.getElementById('checkout-step-3').classList.remove('hidden');
            updatePaymentMethodSelection();
        }

        function completePaymentAndOrder() {
            const subtotal = cart.reduce((acc, i) => acc + (i.price * i.qty), 0);
            const grandTotal = Math.max(0, subtotal + 4.90 - appliedDiscount);

            const firstname = document.getElementById('ship-firstname').value;
            const lastname = document.getElementById('ship-lastname').value;
            const street = document.getElementById('ship-street').value;
            const zip = document.getElementById('ship-zip').value;
            const city = document.getElementById('ship-city').value;

            const selectedMethodEl = document.querySelector('input[name="payment-method"]:checked');
            const paymentType = selectedMethodEl ? selectedMethodEl.value : "Paysafecard";
            const giftCardCode = document.getElementById('checkout-gift-card-code').value.trim();

            if (!paymentType.includes('PayPal') && !giftCardCode) {
                showToast(`Bitte gib den Gutscheincode für deine ${paymentType}-Karte im Wert von €${grandTotal.toFixed(2)} ein!`, "error");
                return;
            }

            const orderId = `#3D-${Math.floor(10000 + Math.random() * 90000)}`;
            const newOrder = {
                id: orderId,
                customerName: `${firstname} ${lastname}`,
                email: currentUser ? currentUser.email : "kunde@beispiel.de",
                items: cart.map(c => `${c.title} (x${c.qty})`).join(', '),
                total: grandTotal,
                paymentMethod: `${paymentType} ${giftCardCode ? '(Code: ' + giftCardCode + ')' : ''}`,
                status: "In Bearbeitung",
                address: `${street}, ${zip} ${city}, Deutschland`,
                date: new Date().toLocaleDateString('de-DE')
            };

            orders.unshift(newOrder);

            document.getElementById('confirm-order-id').innerText = orderId;
            document.getElementById('confirm-email-display').innerText = newOrder.email;
            document.getElementById('confirm-address-display').innerText = `${newOrder.customerName}, ${city}`;

            // Reset cart
            cart = [];
            appliedDiscount = 0;
            document.getElementById('checkout-gift-card-code').value = '';
            updateCartUI();

            document.getElementById('checkout-step-3').classList.add('hidden');
            document.getElementById('checkout-step-4').classList.remove('hidden');

            showToast("Gutschein übermittelt! Die Bestellung ist eingegangen.");
        }

        // ADMIN DASHBOARD TAB MANAGEMENT & TABLES
        function renderAdminTables() {
            // Update KPIs
            const totalRev = orders.reduce((acc, o) => acc + o.total, 0);
            const pendingShipments = orders.filter(o => o.status === 'In Bearbeitung').length;

            document.getElementById('kpi-total-revenue').innerText = `€${totalRev.toFixed(2)}`;
            document.getElementById('kpi-total-orders').innerText = orders.length;
            document.getElementById('kpi-pending-shipments').innerText = pendingShipments;
            document.getElementById('kpi-active-products').innerText = products.length;
            document.getElementById('badge-pending-orders').innerText = pendingShipments;

            // Render Orders Table
            const ordersTable = document.getElementById('admin-orders-table-body');
            ordersTable.innerHTML = orders.map(o => `
                <tr class="hover:bg-slate-50 dark:hover:bg-slate-800/50">
                    <td class="p-3.5 font-mono font-bold text-brand-600">${o.id}</td>
                    <td class="p-3.5">
                        <div class="font-bold">${o.customerName}</div>
                        <div class="text-[10px] text-slate-400">${o.email}</div>
                    </td>
                    <td class="p-3.5 max-w-xs truncate">${o.items}</td>
                    <td class="p-3.5 text-slate-500">${o.address}</td>
                    <td class="p-3.5 font-bold">
                        <div>€${o.total.toFixed(2)}</div>
                        <div class="text-[10px] text-amber-600 font-mono font-normal">${o.paymentMethod || 'Gutschein'}</div>
                    </td>
                    <td class="p-3.5">
                        <span class="px-2 py-0.5 rounded-full text-[10px] font-bold ${o.status === 'Versendet' ? 'bg-emerald-100 text-emerald-700' : 'bg-amber-100 text-amber-700'}">
                            ${o.status}
                        </span>
                    </td>
                    <td class="p-3.5 text-right space-x-1">
                        <button onclick="toggleOrderStatus('${o.id}')" class="px-2 py-1 bg-slate-200 dark:bg-slate-700 hover:bg-slate-300 rounded text-[10px] font-bold">
                            Status ändern
                        </button>
                    </td>
                </tr>
            `).join('');

            // Render Products Table
            const productsTable = document.getElementById('admin-products-table-body');
            productsTable.innerHTML = products.map(p => `
                <tr class="hover:bg-slate-50 dark:hover:bg-slate-800/50">
                    <td class="p-3">
                        <img src="${p.image}" class="w-10 h-10 object-cover rounded-lg">
                    </td>
                    <td class="p-3 font-bold">${p.title}</td>
                    <td class="p-3 text-slate-500">${p.category}</td>
                    <td class="p-3 font-bold">€${p.price.toFixed(2)}</td>
                    <td class="p-3">${p.stock} Stk.</td>
                    <td class="p-3 text-right">
                        <button onclick="deleteProduct(${p.id})" class="text-red-500 hover:text-red-700 font-bold">Löschen</button>
                    </td>
                </tr>
            `).join('');

            // Render Coupons Table
            const couponsTable = document.getElementById('admin-coupons-table-body');
            couponsTable.innerHTML = coupons.map(c => `
                <tr class="hover:bg-slate-50 dark:hover:bg-slate-800/50">
                    <td class="p-3 font-mono font-bold text-purple-600">${c.code}</td>
                    <td class="p-3 font-bold">${c.type === 'percent' ? c.value + '%' : '€' + c.value.toFixed(2)}</td>
                    <td class="p-3 text-slate-500">${c.uses} x eingelöst</td>
                    <td class="p-3 text-right">
                        <button onclick="deleteCoupon('${c.code}')" class="text-red-500 hover:text-red-700 font-bold">Löschen</button>
                    </td>
                </tr>
            `).join('');
        }

        function toggleOrderStatus(orderId) {
            const order = orders.find(o => o.id === orderId);
            if (order) {
                order.status = order.status === 'In Bearbeitung' ? 'Versendet' : 'In Bearbeitung';
                renderAdminTables();
                showToast(`Bestellung ${orderId} auf "${order.status}" aktualisiert.`);
            }
        }

        function deleteProduct(id) {
            products = products.filter(p => p.id !== id);
            renderProducts();
            renderAdminTables();
            showToast("Produkt gelöscht.");
        }

        function deleteCoupon(code) {
            coupons = coupons.filter(c => c.code !== code);
            renderAdminTables();
            showToast("Rabattcode gelöscht.");
        }

        function openAddProductModal() {
            document.getElementById('add-product-modal').classList.remove('hidden');
        }

        function closeAddProductModal() {
            document.getElementById('add-product-modal').classList.add('hidden');
        }

        function saveNewProduct() {
            const title = document.getElementById('new-prod-title').value;
            const price = parseFloat(document.getElementById('new-prod-price').value);
            const cat = document.getElementById('new-prod-cat').value;
            const desc = document.getElementById('new-prod-desc').value;
            const img = document.getElementById('new-prod-img').value || "https://images.unsplash.com/photo-1618005182384-a83a8bd57fbe?w=600&auto=format&fit=crop&q=80";

            if (!title || isNaN(price)) {
                showToast("Bitte mindestens Titel und gültigen Preis angeben", "error");
                return;
            }

            const newProd = {
                id: Date.now(),
                title: title,
                category: cat,
                price: price,
                stock: 10,
                image: img,
                description: desc || "Individueller High-Quality 3D-Druck.",
                material: "PLA / Resin",
                dimensions: "Standard Maße"
            };

            products.push(newProd);
            closeAddProductModal();
            renderProducts();
            renderAdminTables();
            showToast("Neues 3D-Modell erfolgreich hochgeladen!");
        }

        function saveNewCoupon() {
            const code = document.getElementById('coupon-input-code').value.trim().toUpperCase();
            const type = document.getElementById('coupon-input-type').value;
            const val = parseFloat(document.getElementById('coupon-input-value').value);

            if (!code || isNaN(val)) {
                showToast("Bitte gültigen Code und Wert eingeben", "error");
                return;
            }

            coupons.push({ code: code, type: type, value: val, uses: 0 });
            renderAdminTables();
            showToast(`Rabattcode "${code}" erstellt!`);
        }

        // CHART RENDERING WITH CHART.JS
        let salesChartInstance = null;
        function renderChart() {
            const ctx = document.getElementById('salesChart').getContext('2d');
            if (salesChartInstance) {
                salesChartInstance.destroy();
            }

            salesChartInstance = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ['Mai', 'Juni', 'Juli', 'August', 'September (Aktuell)'],
                    datasets: [{
                        label: 'Umsatz (€)',
                        data: [240, 420, 310, 580, 840],
                        backgroundColor: '#0284c7',
                        borderRadius: 8
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { display: false }
                    },
                    scales: {
                        y: { beginAtZero: true }
                    }
                }
            });
        }

        // PRODUCT DETAILS MODAL
        function openProductModal(id) {
            const p = products.find(prod => prod.id === id);
            if (!p) return;

            const modalContent = document.getElementById('product-modal-content');
            modalContent.innerHTML = `
                <div class="h-64 sm:h-auto rounded-2xl overflow-hidden bg-slate-100">
                    <img src="${p.image}" class="w-full h-full object-cover">
                </div>
                <div class="flex flex-col justify-between space-y-4">
                    <div>
                        <span class="text-[10px] font-extrabold uppercase text-brand-600 tracking-wider">${p.category}</span>
                        <h3 class="text-xl font-extrabold mt-1">${p.title}</h3>
                        <p class="text-xs text-slate-500 mt-2 leading-relaxed">${p.description}</p>
                        
                        <div class="mt-4 space-y-2 text-xs">
                            <div class="flex justify-between border-b border-slate-100 dark:border-slate-800 pb-1">
                                <span class="text-slate-400">Filament/Material:</span>
                                <span class="font-bold">${p.material}</span>
                            </div>
                            <div class="flex justify-between border-b border-slate-100 dark:border-slate-800 pb-1">
                                <span class="text-slate-400">Abmessungen:</span>
                                <span class="font-bold">${p.dimensions}</span>
                            </div>
                        </div>
                    </div>

                    <div class="pt-4 border-t border-slate-100 dark:border-slate-800 flex items-center justify-between">
                        <span class="text-2xl font-black text-slate-900 dark:text-white">€${p.price.toFixed(2)}</span>
                        <button onclick="addToCart(${p.id}); closeProductModal();" class="px-5 py-2.5 bg-brand-500 hover:bg-brand-600 text-white rounded-xl text-xs font-bold shadow-md">
                            <i class="fa-solid fa-cart-plus mr-1"></i> In den Warenkorb
                        </button>
                    </div>
                </div>
            `;
            document.getElementById('product-detail-modal').classList.remove('hidden');
        }

        function closeProductModal() {
            document.getElementById('product-detail-modal').classList.add('hidden');
        }

        function openAuthModal() {
            proceedToCheckout();
        }

        // TOAST NOTIFICATIONS
        function showToast(message, type = "success") {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div');
            toast.className = `p-3.5 rounded-2xl shadow-xl text-xs font-bold text-white flex items-center space-x-2 transition-all transform translate-y-2 ${type === 'error' ? 'bg-red-500' : 'bg-slate-900 dark:bg-slate-100 dark:text-slate-900'}`;
            toast.innerHTML = `
                <i class="fa-solid ${type === 'error' ? 'fa-circle-exclamation' : 'fa-circle-check text-emerald-400'}"></i>
                <span>${message}</span>
            `;
            container.appendChild(toast);
            setTimeout(() => {
                toast.remove();
            }, 3000);
        }

        // INITIALIZATION
        window.onload = function() {
            renderProducts();
            updateCartUI();
        };
    </script>
</body>
</html>
