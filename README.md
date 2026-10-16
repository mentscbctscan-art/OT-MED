<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MediFlow OT - Hospital Pharmacy & Surgery Inventory</title>
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Font Awesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <!-- Google Font Inter -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">

  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'sans-serif'],
            mono: ['JetBrains Mono', 'monospace'],
          },
          colors: {
            brand: {
              50: '#eff6ff',
              100: '#dbeafe',
              500: '#2563eb',
              600: '#1d4ed8',
              700: '#1e40af',
              800: '#1e3a8a',
              900: '#172554',
            }
          }
        }
      }
    }
  </script>

  <style>
    body {
      font-family: 'Inter', sans-serif;
      -webkit-font-smoothing: antialiased;
    }
    /* Custom Scrollbars */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: #f1f5f9;
    }
    ::-webkit-scrollbar-thumb {
      background: #cbd5e1;
      border-radius: 9999px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #94a3b8;
    }
    .modal-backdrop {
      background-color: rgba(15, 23, 42, 0.65);
      backdrop-filter: blur(4px);
    }
    /* Print Specific Styles */
    @media print {
      body * {
        visibility: hidden;
      }
      #printable-bill-area, #printable-bill-area * {
        visibility: visible;
      }
      #printable-bill-area {
        position: absolute;
        left: 0;
        top: 0;
        width: 100%;
        margin: 0;
        padding: 0;
        box-shadow: none !important;
        border: none !important;
      }
      .no-print {
        display: none !important;
      }
    }
  </style>
</head>
<body class="bg-slate-50 text-slate-800 antialiased min-h-screen flex flex-col">

  <!-- Toast Notifications Container -->
  <div id="toast-container" class="fixed top-5 right-5 z-[9999] flex flex-col space-y-2 pointer-events-none"></div>

  <!-- Confirmation / Action Modal -->
  <div id="confirm-modal" class="fixed inset-0 z-[9000] hidden items-center justify-center modal-backdrop p-4">
    <div class="bg-white rounded-2xl max-w-md w-full p-6 shadow-2xl border border-slate-100 transform transition-all">
      <div class="flex items-center space-x-3 text-amber-500 mb-3">
        <i class="fa-solid fa-triangle-exclamation text-2xl"></i>
        <h3 class="text-lg font-bold text-slate-900" id="confirm-title">Confirm Action</h3>
      </div>
      <p class="text-sm text-slate-600 mb-6" id="confirm-message">Are you sure you want to proceed?</p>
      <div class="flex justify-end space-x-3">
        <button id="confirm-btn-cancel" class="px-4 py-2 text-sm font-medium rounded-xl text-slate-600 bg-slate-100 hover:bg-slate-200 transition">Cancel</button>
        <button id="confirm-btn-ok" class="px-4 py-2 text-sm font-medium rounded-xl text-white bg-red-600 hover:bg-red-700 transition">Confirm</button>
      </div>
    </div>
  </div>

  <!-- Fullscreen Login Doorway Overlay -->
  <div id="auth-doorway" class="fixed inset-0 z-[8000] bg-gradient-to-br from-slate-900 via-brand-900 to-slate-950 flex items-center justify-center p-4">
    <div class="bg-white/95 backdrop-blur-md max-w-md w-full rounded-3xl shadow-2xl border border-white/20 p-8 relative overflow-hidden">
      <!-- Decorative Header Graphic -->
      <div class="absolute -top-12 -right-12 w-40 h-40 bg-brand-500/10 rounded-full blur-2xl"></div>
      
      <div class="text-center mb-8 relative">
        <div class="inline-flex items-center justify-center w-16 h-16 rounded-2xl bg-brand-600 text-white shadow-lg shadow-brand-500/30 mb-3">
          <i class="fa-solid fa-hospital text-2xl"></i>
        </div>
        <h1 class="text-2xl font-black text-slate-900 tracking-tight" id="doorway-app-name">MEDIFLOW OT SYSTEM</h1>
        <p class="text-xs uppercase tracking-widest text-slate-500 font-semibold mt-1">Surgical Pharmacy Doorway</p>
      </div>

      <!-- Standard Login Form -->
      <form id="login-form" class="space-y-4">
        <div id="standard-auth-fields">
          <div>
            <label class="block text-xs font-bold uppercase tracking-wider text-slate-600 mb-1.5">User ID</label>
            <div class="relative">
              <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-slate-400">
                <i class="fa-solid fa-user-doctor"></i>
              </span>
              <input type="text" id="login-userid" class="w-full pl-10 pr-4 py-2.5 bg-slate-50 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-brand-500 focus:bg-white transition" placeholder="Enter User ID" required autocomplete="username">
            </div>
          </div>

          <div class="mt-4">
            <label class="block text-xs font-bold uppercase tracking-wider text-slate-600 mb-1.5">Password</label>
            <div class="relative">
              <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-slate-400">
                <i class="fa-solid fa-key"></i>
              </span>
              <input type="password" id="login-password" class="w-full pl-10 pr-4 py-2.5 bg-slate-50 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-brand-500 focus:bg-white transition" placeholder="••••••••" required autocomplete="current-password">
            </div>
          </div>
        </div>

        <!-- Master Password Override Field (Hidden by default) -->
        <div id="master-auth-field" class="hidden">
          <div class="bg-amber-50 border border-amber-200 rounded-xl p-3 mb-3 text-xs text-amber-800">
            <i class="fa-solid fa-shield-halved mr-1 text-amber-600"></i>
            Master Emergency Override will bypass regular user credentials.
          </div>
          <label class="block text-xs font-bold uppercase tracking-wider text-amber-900 mb-1.5">Master Override Key</label>
          <div class="relative">
            <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-amber-500">
              <i class="fa-solid fa-unlock-keyhole"></i>
            </span>
            <input type="password" id="login-master-key" class="w-full pl-10 pr-4 py-2.5 bg-amber-50/50 border border-amber-300 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-amber-500 transition" placeholder="Enter Master Key Bypass">
          </div>
        </div>

        <button type="submit" id="btn-submit-login" class="w-full py-3 bg-brand-600 hover:bg-brand-700 text-white font-semibold rounded-xl shadow-lg shadow-brand-500/25 transition flex items-center justify-center space-x-2 mt-2">
          <span>Authorize Access</span>
          <i class="fa-solid fa-arrow-right-to-bracket text-sm"></i>
        </button>

        <div class="pt-3 text-center">
          <button type="button" id="toggle-override-btn" class="text-xs text-brand-600 hover:text-brand-800 font-medium inline-flex items-center space-x-1.5 transition">
            <i class="fa-solid fa-bolt"></i>
            <span id="override-text-label">Override with Master Password</span>
          </button>
        </div>
      </form>
    </div>
  </div>

  <!-- Top Application Navigation Bar -->
  <header class="bg-white border-b border-slate-200 sticky top-0 z-40 shadow-xs">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex items-center justify-between h-16">
        
        <!-- Left: Logo & Portal Title -->
        <div class="flex items-center space-x-3">
          <div class="h-10 w-10 rounded-xl bg-gradient-to-tr from-brand-700 to-brand-500 text-white flex items-center justify-center font-black text-lg shadow-md shadow-brand-500/20">
            <i class="fa-solid fa-notes-medical"></i>
          </div>
          <div>
            <h1 class="text-lg font-bold tracking-tight text-slate-900 leading-tight" id="topbar-hospital-name">AURA MULTISPECIALITY HOSPITAL</h1>
            <p class="text-xs text-slate-500 font-medium flex items-center space-x-1.5">
              <span class="w-2 h-2 rounded-full bg-emerald-500 inline-block animate-pulse"></span>
              <span id="topbar-site-subname">Operation Theatre Pharmacy & Surgical Inventory</span>
            </p>
          </div>
        </div>

        <!-- Center: Primary Module Tabs Navigation -->
        <nav class="hidden md:flex space-x-1 bg-slate-100 p-1.5 rounded-2xl border border-slate-200/80">
          <button id="nav-btn-ot" onclick="switchMainTab('ot')" class="px-5 py-2 text-sm font-semibold rounded-xl transition flex items-center space-x-2 bg-white text-brand-700 shadow-sm">
            <i class="fa-solid fa-capsules"></i>
            <span>OT MEDICINES</span>
          </button>
          <button id="nav-btn-purchase" onclick="switchMainTab('purchase')" class="px-5 py-2 text-sm font-semibold rounded-xl transition flex items-center space-x-2 text-slate-600 hover:text-slate-900">
            <i class="fa-solid fa-cart-flatbed"></i>
            <span>PURCHASE</span>
          </button>
          <button id="nav-btn-vendors" onclick="switchMainTab('vendors')" class="px-5 py-2 text-sm font-semibold rounded-xl transition flex items-center space-x-2 text-slate-600 hover:text-slate-900">
            <i class="fa-solid fa-handshake-angle"></i>
            <span>REPS / VENDORS</span>
          </button>
        </nav>

        <!-- Right: Actions, Notification Bell, 9-dots button, Lock Door -->
        <div class="flex items-center space-x-2.5">
          <!-- Notification Bell Button with Overdue Badge Counter -->
          <button id="btn-notification-bell" onclick="openNotificationCenter()" title="Overdue Deliveries & Payments Notification Center" class="relative w-10 h-10 rounded-xl bg-slate-100 hover:bg-slate-200 text-slate-700 flex items-center justify-center transition border border-slate-200 active:scale-95">
            <i class="fa-solid fa-bell text-base"></i>
            <span id="notif-badge" class="hidden absolute -top-1 -right-1 px-1.5 py-0.2 bg-red-600 text-white text-[10px] font-extrabold rounded-full border-2 border-white shadow-sm animate-pulse">0</span>
          </button>

          <!-- 9-Dots Quick Settings Menu Button -->
          <button id="btn-open-settings" onclick="openSettingsModal()" title="System Apps & Configuration" class="w-10 h-10 rounded-xl bg-slate-100 hover:bg-slate-200 text-slate-700 flex items-center justify-center transition border border-slate-200 active:scale-95">
            <div class="grid grid-cols-3 gap-1 p-1">
              <span class="w-1.5 h-1.5 bg-slate-700 rounded-full"></span>
              <span class="w-1.5 h-1.5 bg-slate-700 rounded-full"></span>
              <span class="w-1.5 h-1.5 bg-slate-700 rounded-full"></span>
              <span class="w-1.5 h-1.5 bg-slate-700 rounded-full"></span>
              <span class="w-1.5 h-1.5 bg-slate-700 rounded-full"></span>
              <span class="w-1.5 h-1.5 bg-slate-700 rounded-full"></span>
              <span class="w-1.5 h-1.5 bg-slate-700 rounded-full"></span>
              <span class="w-1.5 h-1.5 bg-slate-700 rounded-full"></span>
              <span class="w-1.5 h-1.5 bg-slate-700 rounded-full"></span>
            </div>
          </button>

          <!-- Lock Door Session Button -->
          <button onclick="lockDoorway()" title="Lock OT Portal" class="h-10 px-3.5 rounded-xl border border-red-200 bg-red-50 hover:bg-red-100 text-red-700 text-xs font-semibold flex items-center space-x-1.5 transition">
            <i class="fa-solid fa-lock text-xs"></i>
            <span class="hidden sm:inline">Lock Door</span>
          </button>
        </div>

      </div>

      <!-- Mobile Navigation Tabs Strip -->
      <div class="flex md:hidden space-x-1 py-2 border-t border-slate-100 overflow-x-auto">
        <button onclick="switchMainTab('ot')" id="mob-nav-ot" class="px-3 py-1.5 text-xs font-semibold rounded-lg bg-brand-600 text-white whitespace-nowrap">OT MEDICINES</button>
        <button onclick="switchMainTab('purchase')" id="mob-nav-purchase" class="px-3 py-1.5 text-xs font-semibold rounded-lg text-slate-600 hover:bg-slate-100 whitespace-nowrap">PURCHASE</button>
        <button onclick="switchMainTab('vendors')" id="mob-nav-vendors" class="px-3 py-1.5 text-xs font-semibold rounded-lg text-slate-600 hover:bg-slate-100 whitespace-nowrap">REPS / VENDORS</button>
      </div>
    </div>
  </header>

  <!-- MAIN APPLICATION CONTENT -->
  <main class="flex-1 max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6">

    <!-- ========================================== -->
    <!-- SECTION 1: OT MEDICINES DASHBOARD & MODULE -->
    <!-- ========================================== -->
    <section id="module-ot" class="space-y-6">

      <!-- Action Top Bar (4 Top Options: Add Medicine, Sell, Templates, Archive) -->
      <div class="bg-gradient-to-r from-brand-900 to-slate-900 text-white p-5 rounded-3xl shadow-xl flex flex-col lg:flex-row items-center justify-between gap-5">
        <div>
          <span class="px-3 py-0.5 bg-brand-500/20 text-brand-300 border border-brand-400/30 text-[11px] rounded-full uppercase tracking-wider font-semibold">OT Dispensing Desk</span>
          <h2 class="text-xl font-extrabold mt-1 tracking-tight">Operation Theatre Pharmacy</h2>
        </div>

        <!-- 4 Top Action Buttons -->
        <div class="flex flex-wrap items-center gap-2.5 w-full lg:w-auto">
          <!-- 1st Option: Add Medicine -->
          <button onclick="openAddMedicineModal()" class="flex-1 sm:flex-none px-4 py-2.5 rounded-2xl bg-emerald-500 hover:bg-emerald-600 text-white font-bold text-xs shadow-md shadow-emerald-500/30 transition flex items-center justify-center space-x-1.5 active:scale-95">
            <i class="fa-solid fa-circle-plus"></i>
            <span>1. ADD MEDICINE</span>
          </button>
          <!-- 2nd Option: Sell / Billing -->
          <button onclick="openSellModal()" class="flex-1 sm:flex-none px-4 py-2.5 rounded-2xl bg-brand-500 hover:bg-brand-600 text-white font-bold text-xs shadow-md shadow-brand-500/30 transition flex items-center justify-center space-x-1.5 active:scale-95">
            <i class="fa-solid fa-receipt"></i>
            <span>2. SELL</span>
          </button>
          <!-- 3rd Option: Templates -->
          <button onclick="openTemplatesModal()" class="flex-1 sm:flex-none px-4 py-2.5 rounded-2xl bg-purple-600 hover:bg-purple-700 text-white font-bold text-xs shadow-md shadow-purple-600/30 transition flex items-center justify-center space-x-1.5 active:scale-95">
            <i class="fa-solid fa-cubes-stacked"></i>
            <span>3. TEMPLATES</span>
          </button>
          <!-- 4th Option: Archive (Toggles archive bills view) -->
          <button id="btn-ot-archive-toggle" onclick="toggleArchiveView()" class="flex-1 sm:flex-none px-4 py-2.5 rounded-2xl bg-slate-700 hover:bg-slate-600 text-white font-bold text-xs shadow-md transition flex items-center justify-center space-x-1.5 active:scale-95">
            <i class="fa-solid fa-box-archive"></i>
            <span>4. ARCHIVE</span>
          </button>
        </div>
      </div>

      <!-- Quick Metrics Strip -->
      <div class="grid grid-cols-2 sm:grid-cols-4 gap-4">
        <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-xs">
          <p class="text-xs font-medium text-slate-500">Stock Items</p>
          <h4 class="text-xl font-bold text-slate-900 mt-1" id="stat-ot-total-items">0</h4>
        </div>
        <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-xs">
          <p class="text-xs font-medium text-slate-500">Low Stock (&lt; 10)</p>
          <h4 class="text-xl font-bold text-amber-600 mt-1" id="stat-ot-low-stock">0</h4>
        </div>
        <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-xs">
          <p class="text-xs font-medium text-slate-500">This Month Bills</p>
          <h4 class="text-xl font-bold text-brand-600 mt-1" id="stat-ot-month-bills">0</h4>
        </div>
        <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-xs">
          <p class="text-xs font-medium text-slate-500">Templates</p>
          <h4 class="text-xl font-bold text-purple-600 mt-1" id="stat-ot-total-templates">0</h4>
        </div>
      </div>

      <!-- OT Medicines Inventory Live Table Section -->
      <div id="ot-inventory-section" class="bg-white rounded-3xl border border-slate-200 shadow-xs overflow-hidden">
        <div class="p-5 border-b border-slate-100 flex flex-col md:flex-row items-center justify-between gap-4">
          <div class="flex items-center space-x-3 w-full md:w-auto">
            <div class="h-8 w-1 bg-brand-600 rounded-full"></div>
            <div>
              <h3 class="font-bold text-slate-900 text-base">OT Medicine Stock</h3>
              <p class="text-xs text-slate-500">Reflects automatically upon addition or dispensing</p>
            </div>
          </div>
          
          <!-- Search & Filter Controls -->
          <div class="flex items-center space-x-3 w-full md:w-auto">
            <div class="relative flex-1 md:w-72">
              <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400">
                <i class="fa-solid fa-magnifying-glass text-xs"></i>
              </span>
              <input type="text" id="ot-inventory-search" oninput="renderOTInventoryTable()" placeholder="Search medicine, batch, stockist..." class="w-full pl-9 pr-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs focus:outline-none focus:ring-2 focus:ring-brand-500 focus:bg-white transition">
            </div>

            <!-- Export Inventory to Excel Button -->
            <button onclick="exportOTInventoryToExcel()" class="px-3.5 py-2 rounded-xl border border-emerald-300 bg-emerald-50 hover:bg-emerald-100 text-emerald-800 text-xs font-semibold flex items-center space-x-1.5 transition">
              <i class="fa-solid fa-file-excel text-emerald-600"></i>
              <span>Export</span>
            </button>
          </div>
        </div>

        <!-- Inventory Table Container -->
        <div class="overflow-x-auto">
          <table class="w-full text-left border-collapse">
            <thead>
              <tr class="bg-slate-50 text-[11px] font-bold uppercase tracking-wider text-slate-600 border-b border-slate-200">
                <th class="py-3.5 px-4">#</th>
                <th class="py-3.5 px-4">Medicine Name</th>
                <th class="py-3.5 px-4">Stockist</th>
                <th class="py-3.5 px-4">Batch No</th>
                <th class="py-3.5 px-4">MFR / Exp</th>
                <th class="py-3.5 px-4 text-center">Stock Qty</th>
                <th class="py-3.5 px-4 text-right">MRP (₹)</th>
                <th class="py-3.5 px-4 text-center">Actions</th>
              </tr>
            </thead>
            <tbody id="ot-inventory-tbody" class="divide-y divide-slate-100 text-xs font-medium text-slate-700">
              <!-- Dynamically populated -->
            </tbody>
          </table>
        </div>
      </div>

      <!-- OT Bills Archives Section (Shows ONLY when ARCHIVE top option is clicked) -->
      <div id="ot-archive-section" class="bg-white rounded-3xl border border-slate-200 shadow-xs overflow-hidden hidden">
        <div class="p-5 border-b border-slate-100 flex flex-col md:flex-row items-center justify-between gap-4 bg-slate-50/70">
          <div>
            <h3 class="font-bold text-slate-900 text-base flex items-center space-x-2">
              <i class="fa-solid fa-box-archive text-brand-600"></i>
              <span>Archive Bills</span>
            </h3>
            <p class="text-xs text-slate-500">Current month sales by default with calendar date pickers</p>
          </div>

          <!-- Archive Month Picker and Search -->
          <div class="flex flex-wrap items-center gap-3">
            <div class="flex items-center space-x-2">
              <label class="text-xs font-semibold text-slate-600">Month:</label>
              <input type="month" id="archive-month-picker" onchange="renderBillsArchive()" class="px-2.5 py-1.5 bg-white border border-slate-200 rounded-xl text-xs font-medium focus:ring-2 focus:ring-brand-500">
            </div>

            <div class="relative w-52">
              <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400">
                <i class="fa-solid fa-magnifying-glass text-xs"></i>
              </span>
              <input type="text" id="archive-bill-search" oninput="renderBillsArchive()" placeholder="Search patient, UHID, IP..." class="w-full pl-8 pr-3 py-1.5 bg-white border border-slate-200 rounded-xl text-xs">
            </div>

            <button onclick="exportBillsArchiveToExcel()" class="px-3 py-1.5 rounded-xl border border-slate-200 bg-white hover:bg-slate-100 text-slate-700 text-xs font-semibold flex items-center space-x-1 transition">
              <i class="fa-solid fa-download"></i>
              <span>Export CSV</span>
            </button>

            <button onclick="toggleArchiveView(false)" class="p-1.5 text-slate-400 hover:text-slate-700 rounded-lg" title="Hide Archive">
              <i class="fa-solid fa-xmark text-sm"></i>
            </button>
          </div>
        </div>

        <div class="overflow-x-auto">
          <table class="w-full text-left border-collapse">
            <thead>
              <tr class="bg-slate-50 text-[11px] font-bold uppercase tracking-wider text-slate-600 border-b border-slate-200">
                <th class="py-3 px-4">Bill No</th>
                <th class="py-3 px-4">Date</th>
                <th class="py-3 px-4">Patient</th>
                <th class="py-3 px-4">UHID / IP</th>
                <th class="py-3 px-4">Doctor & Surgery</th>
                <th class="py-3 px-4 text-center">Items</th>
                <th class="py-3 px-4 text-right">Total</th>
                <th class="py-3 px-4 text-center">Print</th>
              </tr>
            </thead>
            <tbody id="ot-bills-tbody" class="divide-y divide-slate-100 text-xs font-medium text-slate-700">
              <!-- Dynamically populated -->
            </tbody>
          </table>
        </div>
      </div>

    </section>

    <!-- ========================================== -->
    <!-- SECTION 2: PURCHASE & ORDER MANAGEMENT      -->
    <!-- ========================================== -->
    <section id="module-purchase" class="space-y-6 hidden">
      
      <!-- Purchase Header & Create Order Button -->
      <div class="bg-gradient-to-r from-slate-900 to-teal-950 text-white p-5 rounded-3xl shadow-xl flex flex-col sm:flex-row items-center justify-between gap-4">
        <div>
          <span class="px-3 py-0.5 bg-teal-500/20 text-teal-300 border border-teal-400/30 text-[11px] rounded-full uppercase tracking-wider font-semibold">Procurement</span>
          <h2 class="text-xl font-extrabold mt-1 tracking-tight">Stockist Orders</h2>
        </div>
        <div class="flex items-center space-x-3">
          <button onclick="openCreateOrderModal()" class="px-4 py-2.5 rounded-2xl bg-teal-500 hover:bg-teal-600 text-white font-bold text-xs shadow-md shadow-teal-500/30 transition flex items-center space-x-2">
            <i class="fa-solid fa-cart-plus"></i>
            <span>New Order</span>
          </button>
          <button onclick="exportPurchaseOrdersToExcel()" class="px-4 py-2.5 rounded-2xl border border-teal-400/40 bg-white/10 hover:bg-white/20 text-white font-semibold text-xs transition">
            <i class="fa-solid fa-file-excel mr-1"></i>
            <span>Export</span>
          </button>
        </div>
      </div>

      <!-- Purchase Orders Navigation Filters / Tabs -->
      <div class="bg-white p-3 rounded-2xl border border-slate-200 flex flex-wrap items-center justify-between gap-3">
        <div class="flex flex-wrap gap-1.5" id="purchase-status-tabs">
          <button onclick="filterPurchaseOrders('all')" data-status="all" class="purchase-filter-btn px-3 py-1.5 text-xs font-bold rounded-xl bg-teal-600 text-white">All</button>
          <button onclick="filterPurchaseOrders('pending')" data-status="pending" class="purchase-filter-btn px-3 py-1.5 text-xs font-bold rounded-xl text-slate-600 hover:bg-slate-100">Pending</button>
          <button onclick="filterPurchaseOrders('completed')" data-status="completed" class="purchase-filter-btn px-3 py-1.5 text-xs font-bold rounded-xl text-slate-600 hover:bg-slate-100">Completed</button>
          <button onclick="filterPurchaseOrders('returned')" data-status="returned" class="purchase-filter-btn px-3 py-1.5 text-xs font-bold rounded-xl text-slate-600 hover:bg-slate-100">Returned</button>
          <button onclick="filterPurchaseOrders('cancelled')" data-status="cancelled" class="purchase-filter-btn px-3 py-1.5 text-xs font-bold rounded-xl text-slate-600 hover:bg-slate-100">Cancelled</button>
        </div>

        <div class="flex items-center space-x-3">
          <input type="month" id="purchase-month-filter" onchange="renderPurchaseOrders()" class="px-2.5 py-1.5 bg-slate-50 border border-slate-200 rounded-xl text-xs font-medium">
          <div class="relative w-48">
            <span class="absolute inset-y-0 left-0 flex items-center pl-2.5 text-slate-400"><i class="fa-solid fa-magnifying-glass text-xs"></i></span>
            <input type="text" id="purchase-search" oninput="renderPurchaseOrders()" placeholder="Search order, stockist..." class="w-full pl-8 pr-2.5 py-1.5 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          </div>
        </div>
      </div>

      <!-- Orders List Container -->
      <div id="purchase-orders-list" class="space-y-4">
        <!-- Rendered order cards -->
      </div>
    </section>

    <!-- ========================================== -->
    <!-- SECTION 3: VENDORS / REPS & OFFERS         -->
    <!-- ========================================== -->
    <section id="module-vendors" class="space-y-6 hidden">
      
      <!-- Vendor Header -->
      <div class="bg-gradient-to-r from-slate-900 to-indigo-950 text-white p-5 rounded-3xl shadow-xl flex flex-col sm:flex-row items-center justify-between gap-4">
        <div>
          <span class="px-3 py-0.5 bg-indigo-500/20 text-indigo-300 border border-indigo-400/30 text-[11px] rounded-full uppercase tracking-wider font-semibold">Directory</span>
          <h2 class="text-xl font-extrabold mt-1 tracking-tight">Reps & Offers</h2>
        </div>
        <div class="flex items-center space-x-3">
          <button onclick="openAddVendorModal()" class="px-4 py-2.5 rounded-2xl bg-indigo-500 hover:bg-indigo-600 text-white font-bold text-xs shadow-md shadow-indigo-500/30 transition flex items-center space-x-1.5">
            <i class="fa-solid fa-user-plus"></i>
            <span>Add Rep</span>
          </button>
          <button onclick="openAddOfferModal()" class="px-4 py-2.5 rounded-2xl bg-purple-600 hover:bg-purple-700 text-white font-bold text-xs shadow-md shadow-purple-600/30 transition flex items-center space-x-1.5">
            <i class="fa-solid fa-tag"></i>
            <span>Add Offer</span>
          </button>
        </div>
      </div>

      <!-- Dual Smart Search Bar for Reps & Products -->
      <div class="bg-white p-4 rounded-3xl border border-slate-200 shadow-xs">
        <label class="block text-xs font-bold uppercase tracking-wider text-slate-700 mb-2">
          <i class="fa-solid fa-magnifying-glass mr-1.5 text-indigo-600"></i>
          Search by Medicine Name, Rep Name, or Company
        </label>
        <div class="relative">
          <input type="text" id="vendor-smart-search" oninput="renderVendorsAndOffers()" placeholder="Search medicine, rep name, company..." class="w-full pl-4 pr-10 py-2.5 bg-slate-50 border border-slate-300 rounded-2xl text-xs focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:bg-white transition">
          <span class="absolute inset-y-0 right-0 flex items-center pr-3.5 text-slate-400">
            <i class="fa-solid fa-filter text-xs"></i>
          </span>
        </div>
      </div>

      <!-- Split View: Medical Reps Directory & Offers -->
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
        
        <!-- Left: Medical Reps Directory -->
        <div class="bg-white rounded-3xl border border-slate-200 shadow-xs p-5">
          <div class="flex items-center justify-between mb-4">
            <h3 class="font-bold text-slate-900 text-sm flex items-center space-x-2">
              <i class="fa-solid fa-id-badge text-indigo-600"></i>
              <span>REPS / VENDORS</span>
            </h3>
            <span class="text-xs font-semibold text-slate-500 bg-slate-100 px-2.5 py-1 rounded-full" id="vendor-count-badge">0 Reps</span>
          </div>

          <div id="vendor-list-container" class="space-y-3.5 max-h-[600px] overflow-y-auto pr-1">
            <!-- Rendered Rep Cards -->
          </div>
        </div>

        <!-- Right: Rep Offers -->
        <div class="bg-white rounded-3xl border border-slate-200 shadow-xs p-5">
          <div class="flex items-center justify-between mb-4">
            <h3 class="font-bold text-slate-900 text-sm flex items-center space-x-2">
              <i class="fa-solid fa-tags text-purple-600"></i>
              <span>REP OFFERS</span>
            </h3>
            <span class="text-xs font-semibold text-purple-700 bg-purple-50 px-2.5 py-1 rounded-full" id="offer-count-badge">0 Offers</span>
          </div>

          <div id="offers-list-container" class="space-y-3.5 max-h-[600px] overflow-y-auto pr-1">
            <!-- Rendered Deals Cards -->
          </div>
        </div>

      </div>

    </section>

  </main>

  <!-- 9-DOTS MODAL: SYSTEM SETTINGS & MASTERS -->
  <div id="modal-settings" class="fixed inset-0 z-[7000] hidden items-center justify-center modal-backdrop p-4 overflow-y-auto">
    <div class="bg-white rounded-3xl max-w-3xl w-full shadow-2xl border border-slate-100 my-8 overflow-hidden">
      <!-- Modal Header -->
      <div class="bg-slate-900 text-white px-6 py-4 flex items-center justify-between">
        <div class="flex items-center space-x-2.5">
          <div class="grid grid-cols-3 gap-1 p-1 bg-white/10 rounded-lg">
            <span class="w-1.5 h-1.5 bg-white rounded-full"></span>
            <span class="w-1.5 h-1.5 bg-white rounded-full"></span>
            <span class="w-1.5 h-1.5 bg-white rounded-full"></span>
            <span class="w-1.5 h-1.5 bg-white rounded-full"></span>
            <span class="w-1.5 h-1.5 bg-white rounded-full"></span>
            <span class="w-1.5 h-1.5 bg-white rounded-full"></span>
            <span class="w-1.5 h-1.5 bg-white rounded-full"></span>
            <span class="w-1.5 h-1.5 bg-white rounded-full"></span>
            <span class="w-1.5 h-1.5 bg-white rounded-full"></span>
          </div>
          <h3 class="text-base font-bold">System Settings & Masters</h3>
        </div>
        <button onclick="closeSettingsModal()" class="text-slate-400 hover:text-white transition p-1">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>

      <!-- Settings Navigation Tabs -->
      <div class="border-b border-slate-200 bg-slate-50 px-6 flex space-x-4 overflow-x-auto text-xs font-bold text-slate-600">
        <button onclick="switchSettingsTab('letterhead')" id="stab-letterhead" class="py-3 border-b-2 border-brand-600 text-brand-600 whitespace-nowrap">1. Hospital Letterhead</button>
        <button onclick="switchSettingsTab('doctors')" id="stab-doctors" class="py-3 border-b-2 border-transparent hover:text-slate-900 whitespace-nowrap">2. Manage Doctors</button>
        <button onclick="switchSettingsTab('stockists')" id="stab-stockists" class="py-3 border-b-2 border-transparent hover:text-slate-900 whitespace-nowrap">3. Stockists</button>
        <button onclick="switchSettingsTab('credentials')" id="stab-credentials" class="py-3 border-b-2 border-transparent hover:text-slate-900 whitespace-nowrap">4. Credentials</button>
        <button onclick="switchSettingsTab('appname')" id="stab-appname" class="py-3 border-b-2 border-transparent hover:text-slate-900 whitespace-nowrap">5. Portal Name</button>
      </div>

      <!-- Modal Body -->
      <div class="p-6 max-h-[70vh] overflow-y-auto">

        <!-- TAB 1: Letterhead Settings -->
        <div id="spane-letterhead" class="space-y-4">
          <div class="bg-blue-50 border border-blue-200 p-3 rounded-xl text-xs text-blue-800">
            <i class="fa-solid fa-info-circle mr-1"></i> Configure hospital letterhead for billing. You can either use custom text headers OR upload an image/logo letterhead. Only 1 mode is active at a time.
          </div>

          <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
            <div>
              <label class="block text-xs font-bold text-slate-700 mb-1">Letterhead Mode</label>
              <select id="set-letterhead-mode" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
                <option value="text">Formatted Text Header (Hospital Name & Address)</option>
                <option value="image">Graphic Image Letterhead Header</option>
              </select>
            </div>
            <div>
              <label class="block text-xs font-bold text-slate-700 mb-1">Hospital / Clinic Name</label>
              <input type="text" id="set-hosp-name" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
            </div>
            <div class="sm:col-span-2">
              <label class="block text-xs font-bold text-slate-700 mb-1">Hospital Address</label>
              <input type="text" id="set-hosp-address" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
            </div>
            <div>
              <label class="block text-xs font-bold text-slate-700 mb-1">Contact Phone / Mobile</label>
              <input type="text" id="set-hosp-phone" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
            </div>
            <div>
              <label class="block text-xs font-bold text-slate-700 mb-1">Email / GST Number</label>
              <input type="text" id="set-hosp-email" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
            </div>
            <div class="sm:col-span-2">
              <label class="block text-xs font-bold text-slate-700 mb-1">Letterhead Image Upload (JPEG/PNG)</label>
              <input type="file" id="set-hosp-logo-file" accept="image/*" class="w-full text-xs text-slate-500 file:mr-3 file:py-2 file:px-4 file:rounded-xl file:border-0 file:text-xs file:font-semibold file:bg-brand-50 file:text-brand-700 hover:file:bg-brand-100">
              <div id="letterhead-image-preview" class="mt-2 border rounded-xl p-2 bg-slate-50 text-center max-h-32 overflow-hidden flex items-center justify-center">
                <span class="text-xs text-slate-400">No image uploaded</span>
              </div>
            </div>
          </div>
          <button onclick="saveLetterheadSettings()" class="px-5 py-2.5 bg-brand-600 hover:bg-brand-700 text-white rounded-xl text-xs font-bold transition">Save Letterhead Settings</button>
        </div>

        <!-- TAB 2: Manage Doctors -->
        <div id="spane-doctors" class="space-y-4 hidden">
          <div class="flex space-x-2">
            <input type="text" id="new-doctor-name" placeholder="Dr. Name (e.g. Dr. Rajesh Sharma, MS Ortho)" class="flex-1 px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
            <button onclick="addDoctor()" class="px-4 py-2 bg-emerald-600 hover:bg-emerald-700 text-white rounded-xl text-xs font-bold transition">Add Doctor</button>
          </div>
          <div class="border rounded-2xl overflow-hidden">
            <table class="w-full text-left text-xs">
              <thead class="bg-slate-50 font-bold border-b text-slate-600">
                <tr><th class="p-3">Doctor Name</th><th class="p-3 text-right">Actions</th></tr>
              </thead>
              <tbody id="doctors-list-tbody" class="divide-y divide-slate-100">
                <!-- Injected -->
              </tbody>
            </table>
          </div>
        </div>

        <!-- TAB 3: Manage Stockists -->
        <div id="spane-stockists" class="space-y-4 hidden">
          <div class="flex space-x-2">
            <input type="text" id="new-stockist-name" placeholder="Stockist Name (e.g. Apollo Med Pharma Ltd)" class="flex-1 px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
            <button onclick="addStockist()" class="px-4 py-2 bg-emerald-600 hover:bg-emerald-700 text-white rounded-xl text-xs font-bold transition">Add Stockist</button>
          </div>
          <div class="border rounded-2xl overflow-hidden">
            <table class="w-full text-left text-xs">
              <thead class="bg-slate-50 font-bold border-b text-slate-600">
                <tr><th class="p-3">Stockist Company Name</th><th class="p-3 text-right">Actions</th></tr>
              </thead>
              <tbody id="stockists-list-tbody" class="divide-y divide-slate-100">
                <!-- Injected -->
              </tbody>
            </table>
          </div>
        </div>

        <!-- TAB 4: Credentials Manager -->
        <div id="spane-credentials" class="space-y-4 hidden">
          <div class="bg-amber-50 border border-amber-200 p-3 rounded-xl text-xs text-amber-800">
            Modify login credentials and the master key override.
          </div>
          <div class="grid grid-cols-1 sm:grid-cols-3 gap-3">
            <div>
              <label class="block text-xs font-bold text-slate-700 mb-1">User ID</label>
              <input type="text" id="set-cred-userid" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs font-mono">
            </div>
            <div>
              <label class="block text-xs font-bold text-slate-700 mb-1">Standard Password</label>
              <input type="text" id="set-cred-pass" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs font-mono">
            </div>
            <div>
              <label class="block text-xs font-bold text-amber-800 mb-1">Master Override Key</label>
              <input type="text" id="set-cred-master" class="w-full px-3 py-2 bg-amber-50 border border-amber-300 rounded-xl text-xs font-mono">
            </div>
          </div>
          <button onclick="saveCredentialsSettings()" class="px-5 py-2.5 bg-brand-600 hover:bg-brand-700 text-white rounded-xl text-xs font-bold transition">Update Credentials</button>
        </div>

        <!-- TAB 5: App Name Settings -->
        <div id="spane-appname" class="space-y-4 hidden">
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Website / Clinic Display Name</label>
            <input type="text" id="set-portal-title" placeholder="AURA MULTISPECIALITY HOSPITAL" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Sub-heading / Tagline</label>
            <input type="text" id="set-portal-subtitle" placeholder="OT Pharmacy & Surgery Management" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          </div>
          <button onclick="savePortalNameSettings()" class="px-5 py-2.5 bg-brand-600 hover:bg-brand-700 text-white rounded-xl text-xs font-bold transition">Update Branding</button>
        </div>

      </div>
    </div>
  </div>

  <!-- MODAL 1: ADD MEDICINE TO OT -->
  <div id="modal-add-medicine" class="fixed inset-0 z-[6000] hidden items-center justify-center modal-backdrop p-4 overflow-y-auto">
    <div class="bg-white rounded-3xl max-w-xl w-full shadow-2xl border border-slate-100 my-8 overflow-hidden">
      <div class="bg-emerald-600 text-white px-6 py-4 flex items-center justify-between">
        <div class="flex items-center space-x-2">
          <i class="fa-solid fa-circle-plus text-lg"></i>
          <h3 class="text-base font-bold" id="add-med-modal-title">Add OT Medicine</h3>
        </div>
        <button onclick="closeAddMedicineModal()" class="text-emerald-100 hover:text-white p-1">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>

      <form id="form-add-medicine" onsubmit="handleSaveMedicine(event)" class="p-6 space-y-4">
        <input type="hidden" id="med-edit-id">

        <div>
          <label class="block text-xs font-bold text-slate-700 mb-1">Medicine Name <span class="text-red-500">*</span></label>
          <input type="text" id="med-name" placeholder="e.g. Propofol 10mg/ml 20ml Inj" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs focus:ring-2 focus:ring-emerald-500">
        </div>

        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Stockist Name <span class="text-red-500">*</span></label>
            <select id="med-stockist" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs focus:ring-2 focus:ring-emerald-500">
              <!-- Dynamically populated from Stockists list -->
            </select>
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Batch Number <span class="text-red-500">*</span></label>
            <input type="text" id="med-batch" placeholder="e.g. BT-9812A" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs font-mono">
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">MFR / Exp Date <span class="text-red-500">*</span></label>
            <input type="text" id="med-mfr" placeholder="e.g. 09/2024 - 08/2026" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Quantity (Stock) <span class="text-red-500">*</span></label>
            <input type="number" id="med-qty" min="0" placeholder="50" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          </div>
          <div class="sm:col-span-2">
            <label class="block text-xs font-bold text-slate-700 mb-1">MRP per Unit (₹) <span class="text-red-500">*</span></label>
            <input type="number" step="0.01" id="med-mrp" min="0" placeholder="250.00" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          </div>
        </div>

        <div class="bg-slate-50 p-3 rounded-xl border border-slate-200 text-xs text-slate-500">
          <i class="fa-solid fa-circle-info mr-1 text-emerald-600"></i> Adding/updating medicine reflects immediately in OT inventory. The modal stays open until you click <strong>Close</strong>.
        </div>

        <div class="flex items-center justify-between pt-2">
          <button type="button" onclick="closeAddMedicineModal()" class="px-5 py-2.5 rounded-xl border border-slate-300 text-slate-700 font-semibold text-xs hover:bg-slate-100 transition">
            Close
          </button>
          <button type="submit" class="px-6 py-2.5 bg-emerald-600 hover:bg-emerald-700 text-white font-bold rounded-xl text-xs shadow-md shadow-emerald-500/30 transition flex items-center space-x-1.5">
            <i class="fa-solid fa-floppy-disk"></i>
            <span id="btn-save-med-text">Add & Keep In Page</span>
          </button>
        </div>
      </form>
    </div>
  </div>

  <!-- MODAL 2: SELL / BILLING MODAL -->
  <div id="modal-sell" class="fixed inset-0 z-[6000] hidden items-center justify-center modal-backdrop p-4 overflow-y-auto">
    <div class="bg-white rounded-3xl max-w-4xl w-full shadow-2xl border border-slate-100 my-8 overflow-hidden">
      
      <!-- Sell Modal Header -->
      <div class="bg-brand-700 text-white px-6 py-4 flex items-center justify-between">
        <div class="flex items-center space-x-2">
          <i class="fa-solid fa-receipt text-lg"></i>
          <div>
            <h3 class="text-base font-bold">OT Surgical Billing Desk</h3>
            <p class="text-[11px] text-brand-200">Dispense medicines, apply templates, validate details & print bills</p>
          </div>
        </div>
        <div class="flex items-center space-x-2">
          <!-- Load Template Quick Shortcut -->
          <button type="button" onclick="promptApplyTemplateInSell()" class="px-3 py-1.5 bg-brand-800 hover:bg-brand-900 border border-brand-500/50 rounded-xl text-xs font-semibold text-white flex items-center space-x-1.5">
            <i class="fa-solid fa-box-open text-xs"></i>
            <span>Load Surgery Template</span>
          </button>
          <button onclick="closeSellModal()" class="text-brand-200 hover:text-white p-1">
            <i class="fa-solid fa-xmark text-lg"></i>
          </button>
        </div>
      </div>

      <div class="p-6 space-y-6 max-h-[80vh] overflow-y-auto">

        <!-- Patient Demographics Section -->
        <div class="bg-slate-50 p-4 rounded-2xl border border-slate-200">
          <h4 class="text-xs font-bold uppercase tracking-wider text-brand-800 mb-3 flex items-center space-x-1.5">
            <i class="fa-solid fa-user-injured text-xs"></i>
            <span>Patient & Surgery Information</span>
          </h4>

          <div class="grid grid-cols-2 sm:grid-cols-4 gap-3">
            <div>
              <label class="block text-[11px] font-bold text-slate-600 mb-1">Prefix</label>
              <select id="bill-prefix" class="w-full px-2.5 py-1.5 bg-white border border-slate-200 rounded-xl text-xs">
                <option value="Mr.">Mr.</option>
                <option value="Mrs.">Mrs.</option>
                <option value="Ms.">Ms.</option>
                <option value="Master">Master</option>
                <option value="Baby">Baby</option>
                <option value="Dr.">Dr.</option>
              </select>
            </div>
            <div class="col-span-1 sm:col-span-2">
              <label class="block text-[11px] font-bold text-slate-600 mb-1">Patient Name <span class="text-red-500">*</span></label>
              <input type="text" id="bill-patient-name" placeholder="Full Name" class="w-full px-2.5 py-1.5 bg-white border border-slate-200 rounded-xl text-xs">
            </div>
            <div>
              <label class="block text-[11px] font-bold text-slate-600 mb-1">Age</label>
              <input type="number" id="bill-age" placeholder="Age" class="w-full px-2.5 py-1.5 bg-white border border-slate-200 rounded-xl text-xs">
            </div>

            <div>
              <label class="block text-[11px] font-bold text-slate-600 mb-1">Sex</label>
              <select id="bill-sex" class="w-full px-2.5 py-1.5 bg-white border border-slate-200 rounded-xl text-xs">
                <option value="Male">Male</option>
                <option value="Female">Female</option>
                <option value="Other">Other</option>
              </select>
            </div>

            <div>
              <label class="block text-[11px] font-bold text-slate-600 mb-1">Mobile No (10-Digit) <span class="text-red-500">*</span></label>
              <input type="tel" id="bill-mobile" maxlength="10" placeholder="Valid 10 digits" class="w-full px-2.5 py-1.5 bg-white border border-slate-200 rounded-xl text-xs font-mono">
            </div>

            <div>
              <label class="block text-[11px] font-bold text-slate-600 mb-1">IP Number</label>
              <input type="text" id="bill-ip" placeholder="e.g. IP-4402" class="w-full px-2.5 py-1.5 bg-white border border-slate-200 rounded-xl text-xs font-mono">
            </div>

            <div>
              <label class="block text-[11px] font-bold text-slate-600 mb-1">UHID</label>
              <input type="text" id="bill-uhid" placeholder="e.g. UH-90312" class="w-full px-2.5 py-1.5 bg-white border border-slate-200 rounded-xl text-xs font-mono">
            </div>

            <div class="col-span-1 sm:col-span-2">
              <label class="block text-[11px] font-bold text-slate-600 mb-1">Operating Doctor / Surgeon</label>
              <select id="bill-doctor" class="w-full px-2.5 py-1.5 bg-white border border-slate-200 rounded-xl text-xs">
                <!-- Dynamically populated from Doctors List -->
              </select>
            </div>

            <div>
              <label class="block text-[11px] font-bold text-slate-600 mb-1">Billing Date</label>
              <input type="date" id="bill-date" class="w-full px-2.5 py-1.5 bg-white border border-slate-200 rounded-xl text-xs">
            </div>

            <div>
              <label class="block text-[11px] font-bold text-slate-600 mb-1">Surgery / Procedure Name</label>
              <input type="text" id="bill-surgery" placeholder="e.g. Laparoscopic Appendectomy" class="w-full px-2.5 py-1.5 bg-white border border-slate-200 rounded-xl text-xs">
            </div>
          </div>
        </div>

        <!-- Medicine Search-and-Click Dispensing Bar -->
        <div class="space-y-2">
          <label class="block text-xs font-bold text-slate-700">
            Search Medicine to Add (Click to Select item - Not standard dropdown):
          </label>
          <div class="relative">
            <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400">
              <i class="fa-solid fa-magnifying-glass text-xs"></i>
            </span>
            <input type="text" id="sell-med-search" oninput="handleSellMedSearch(this.value)" placeholder="Type medicine name or batch to search in live stock..." class="w-full pl-9 pr-4 py-2 bg-slate-50 border border-slate-300 rounded-xl text-xs focus:ring-2 focus:ring-brand-500 focus:bg-white transition">
          </div>

          <!-- Clickable Search Results Box (Dynamic) -->
          <div id="sell-search-results" class="hidden border border-slate-200 rounded-2xl bg-white shadow-xl max-h-48 overflow-y-auto divide-y divide-slate-100 p-1">
            <!-- Results injected here -->
          </div>
        </div>

        <!-- Added Medicines Table for Bill -->
        <div class="border border-slate-200 rounded-2xl overflow-hidden">
          <div class="bg-slate-100 px-4 py-2.5 flex items-center justify-between border-b border-slate-200">
            <span class="text-xs font-bold text-slate-700 uppercase">Bill Items Breakdown</span>
            <span class="text-xs font-medium text-slate-500" id="bill-items-count">0 items added</span>
          </div>
          <table class="w-full text-left text-xs">
            <thead class="bg-slate-50 text-[11px] font-bold uppercase text-slate-600 border-b">
              <tr>
                <th class="p-3">Medicine</th>
                <th class="p-3">Batch</th>
                <th class="p-3 text-right">MRP (₹)</th>
                <th class="p-3 text-center w-28">Qty</th>
                <th class="p-3 text-right">Subtotal (₹)</th>
                <th class="p-3 text-center">Action</th>
              </tr>
            </thead>
            <tbody id="bill-items-tbody" class="divide-y divide-slate-100 font-medium">
              <tr><td colspan="6" class="p-6 text-center text-slate-400">No medicines added yet. Use the search bar above or load a template.</td></tr>
            </tbody>
          </table>
        </div>

        <!-- Live Totals and Words -->
        <div class="bg-slate-900 text-white p-4 rounded-2xl flex flex-col sm:flex-row items-center justify-between gap-3">
          <div>
            <span class="text-[11px] uppercase tracking-wider text-slate-400 font-semibold">Total Amount in Words</span>
            <p class="text-xs font-medium text-emerald-400 italic mt-0.5" id="bill-amount-words">Zero Rupees Only</p>
          </div>
          <div class="text-right">
            <span class="text-[11px] uppercase tracking-wider text-slate-400 font-semibold">Total Net Amount</span>
            <h3 class="text-2xl font-black text-white" id="bill-total-amount">₹ 0.00</h3>
          </div>
        </div>

        <!-- Print & Sell Decision Bar (2 Options) -->
        <div class="bg-slate-50 border border-slate-200 p-4 rounded-2xl flex flex-col sm:flex-row items-center justify-between gap-4">
          <div class="text-xs text-slate-600">
            <i class="fa-solid fa-print mr-1 text-brand-600"></i> Choose printing mode for final invoice:
          </div>

          <div class="flex items-center space-x-3 w-full sm:w-auto">
            <!-- Sell Option 1: With Letterhead -->
            <button type="button" onclick="submitSellBill('with_letterhead')" class="flex-1 sm:flex-none px-4 py-2.5 rounded-xl bg-brand-600 hover:bg-brand-700 text-white font-bold text-xs shadow-md shadow-brand-500/20 transition flex items-center justify-center space-x-1.5">
              <i class="fa-solid fa-stamp"></i>
              <span>1. Sell WITH Letterhead</span>
            </button>
            <!-- Sell Option 2: Without Letterhead -->
            <button type="button" onclick="submitSellBill('without_letterhead')" class="flex-1 sm:flex-none px-4 py-2.5 rounded-xl bg-slate-800 hover:bg-slate-900 text-white font-bold text-xs shadow-md transition flex items-center justify-center space-x-1.5">
              <i class="fa-solid fa-file-invoice"></i>
              <span>2. Sell WITHOUT Letterhead</span>
            </button>
          </div>
        </div>

      </div>
    </div>
  </div>

  <!-- MODAL 3: TEMPLATES & PACKAGES -->
  <div id="modal-templates" class="fixed inset-0 z-[6000] hidden items-center justify-center modal-backdrop p-4 overflow-y-auto">
    <div class="bg-white rounded-3xl max-w-3xl w-full shadow-2xl border border-slate-100 my-8 overflow-hidden">
      
      <!-- Template Modal Header -->
      <div class="bg-purple-700 text-white px-6 py-4 flex items-center justify-between">
        <div class="flex items-center space-x-2">
          <i class="fa-solid fa-cubes-stacked text-lg"></i>
          <div>
            <h3 class="text-base font-bold">Surgery Templates</h3>
            <p class="text-[11px] text-purple-200">Create surgery kits, inject into billing with one click</p>
          </div>
        </div>
        <button onclick="closeTemplatesModal()" class="text-purple-200 hover:text-white p-1">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>

      <div class="p-6 space-y-6 max-h-[75vh] overflow-y-auto">
        <!-- Create New Template Section -->
        <div class="bg-purple-50/60 border border-purple-200 rounded-2xl p-4 space-y-3">
          <h4 class="text-xs font-bold uppercase text-purple-900 flex items-center space-x-1.5">
            <i class="fa-solid fa-plus-circle"></i>
            <span id="tpl-editor-title">Create Template</span>
          </h4>
          <input type="hidden" id="tpl-edit-id">

          <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
            <div>
              <label class="block text-[11px] font-bold text-slate-700 mb-1">Package Name</label>
              <input type="text" id="tpl-name" placeholder="e.g. Laparoscopic Cholecystectomy Kit" class="w-full px-3 py-2 bg-white border border-slate-200 rounded-xl text-xs">
            </div>
            <div>
              <label class="block text-[11px] font-bold text-slate-700 mb-1">Department</label>
              <input type="text" id="tpl-dept" placeholder="General, Ortho, ENT..." class="w-full px-3 py-2 bg-white border border-slate-200 rounded-xl text-xs">
            </div>
          </div>

          <!-- Add medicines to template draft via Search-and-Click bar (NO DROPDOWN) -->
          <div class="space-y-2 pt-2 border-t border-purple-200/50">
            <label class="block text-[11px] font-bold text-slate-700">Search Medicine to Add to Template (Click to Add):</label>
            <div class="flex items-center space-x-2">
              <div class="relative flex-1">
                <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400">
                  <i class="fa-solid fa-magnifying-glass text-xs"></i>
                </span>
                <input type="text" id="tpl-med-search" oninput="handleTemplateMedSearch(this.value)" placeholder="Type medicine or batch name..." class="w-full pl-9 pr-3 py-2 bg-white border border-purple-200 rounded-xl text-xs focus:ring-2 focus:ring-purple-500">
              </div>
              <input type="number" id="tpl-pick-qty" min="1" value="1" placeholder="Qty" class="w-20 px-3 py-2 bg-white border border-purple-200 rounded-xl text-xs text-center font-bold" title="Default Quantity">
            </div>

            <!-- Clickable Search Results Box for Templates -->
            <div id="tpl-search-results" class="hidden border border-purple-200 rounded-2xl bg-white shadow-xl max-h-44 overflow-y-auto divide-y divide-slate-100 p-1">
              <!-- Search hits injected here -->
            </div>
          </div>

          <!-- Current Draft Items Preview -->
          <div class="border border-purple-200 rounded-xl bg-white overflow-hidden">
            <table class="w-full text-left text-xs">
              <thead class="bg-purple-100/60 font-bold text-purple-900 border-b">
                <tr><th class="p-2.5">Medicine</th><th class="p-2.5 text-center">Default Qty</th><th class="p-2.5 text-right">MRP (₹)</th><th class="p-2.5 text-center">Action</th></tr>
              </thead>
              <tbody id="tpl-draft-tbody" class="divide-y divide-slate-100 font-medium">
                <tr><td colspan="4" class="p-4 text-center text-slate-400">No items added to this package yet.</td></tr>
              </tbody>
            </table>
          </div>

          <div class="flex justify-end space-x-2 pt-1">
            <button type="button" onclick="resetTemplateEditor()" class="px-3.5 py-1.5 rounded-xl border border-slate-300 text-slate-600 text-xs font-semibold">Reset</button>
            <button type="button" onclick="saveSurgeryTemplate()" class="px-5 py-1.5 bg-purple-600 hover:bg-purple-700 text-white rounded-xl text-xs font-bold">Save Package</button>
          </div>
        </div>

        <!-- Existing Templates List -->
        <div>
          <h4 class="text-xs font-bold uppercase tracking-wider text-slate-700 mb-3">Saved Packages</h4>
          <div id="templates-list-container" class="space-y-3">
            <!-- Dynamically populated -->
          </div>
        </div>
      </div>
    </div>
  </div>

  <!-- MODAL: CREATE PURCHASE ORDER (Unit Rate completely removed) -->
  <div id="modal-create-order" class="fixed inset-0 z-[6000] hidden items-center justify-center modal-backdrop p-4 overflow-y-auto">
    <div class="bg-white rounded-3xl max-w-3xl w-full shadow-2xl border border-slate-100 my-8 overflow-hidden">
      <div class="bg-teal-700 text-white px-6 py-4 flex items-center justify-between">
        <div class="flex items-center space-x-2">
          <i class="fa-solid fa-cart-plus text-lg"></i>
          <h3 class="text-base font-bold">New Purchase Order</h3>
        </div>
        <button onclick="closeCreateOrderModal()" class="text-teal-200 hover:text-white p-1">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>

      <div class="p-6 space-y-4 max-h-[75vh] overflow-y-auto">
        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Select Stockist <span class="text-red-500">*</span></label>
            <select id="po-stockist" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
              <!-- Dynamically populated from Stockists -->
            </select>
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Order Date</label>
            <input type="date" id="po-date" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          </div>
        </div>

        <!-- Add Items in Order (NO UNIT RATE FIELD) -->
        <div class="bg-teal-50/50 p-3.5 rounded-2xl border border-teal-200 space-y-3">
          <h5 class="text-xs font-bold uppercase text-teal-900">Add Order Medicines</h5>
          <div class="grid grid-cols-1 sm:grid-cols-3 gap-2">
            <input type="text" id="po-item-name" placeholder="Medicine Name" class="sm:col-span-2 px-3 py-2 bg-white border border-slate-200 rounded-xl text-xs">
            <input type="number" id="po-item-qty" min="1" placeholder="Quantity" class="px-3 py-2 bg-white border border-slate-200 rounded-xl text-xs font-bold text-center">
          </div>
          <button type="button" onclick="addItemToOrderDraft()" class="w-full py-2 bg-teal-600 hover:bg-teal-700 text-white rounded-xl text-xs font-bold transition">
            + Append Medicine to Order
          </button>
        </div>

        <!-- Draft items table (No Rate or Total column) -->
        <div class="border border-slate-200 rounded-xl overflow-hidden">
          <table class="w-full text-left text-xs">
            <thead class="bg-slate-50 font-bold border-b text-slate-600">
              <tr>
                <th class="p-2.5">#</th>
                <th class="p-2.5">Medicine Name</th>
                <th class="p-2.5 text-center">Quantity</th>
                <th class="p-2.5 text-center">Action</th>
              </tr>
            </thead>
            <tbody id="po-draft-tbody" class="divide-y divide-slate-100 font-medium">
              <tr><td colspan="4" class="p-4 text-center text-slate-400">No items added to this order yet.</td></tr>
            </tbody>
          </table>
        </div>

        <div class="flex items-center justify-between pt-2">
          <div class="text-xs font-bold text-slate-600" id="po-draft-total">Total Items: 0</div>
          <button type="button" onclick="submitPurchaseOrder()" class="px-6 py-2.5 bg-teal-600 hover:bg-teal-700 text-white rounded-xl text-xs font-bold shadow-md transition">
            Submit Purchase Order
          </button>
        </div>
      </div>
    </div>
  </div>

  <!-- MODAL: RECORD ORDER PAYMENT (Screenshot is completely optional) -->
  <div id="modal-record-payment" class="fixed inset-0 z-[6500] hidden items-center justify-center modal-backdrop p-4">
    <div class="bg-white rounded-3xl max-w-md w-full shadow-2xl border border-slate-100 p-6 space-y-4">
      <div class="flex items-center space-x-2 text-emerald-700">
        <i class="fa-solid fa-credit-card text-xl"></i>
        <h3 class="text-base font-bold">Record Order Payment</h3>
      </div>
      <input type="hidden" id="pay-po-number">

      <div>
        <label class="block text-xs font-bold text-slate-700 mb-1">Payment Mode</label>
        <select id="pay-mode" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          <option value="UPI">UPI</option>
          <option value="Bank Transfer / NEFT">Bank Transfer / NEFT</option>
          <option value="Cash">Cash</option>
          <option value="Cheque">Cheque</option>
        </select>
      </div>

      <div>
        <label class="block text-xs font-bold text-slate-700 mb-1">Payment Date</label>
        <input type="date" id="pay-date" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
      </div>

      <div>
        <label class="block text-xs font-bold text-slate-700 mb-1">
          Payment Proof Screenshot <span class="text-slate-400 font-normal">(Optional)</span>
        </label>
        <input type="file" id="pay-screenshot-file" accept="image/*" class="w-full text-xs text-slate-500 file:mr-3 file:py-1.5 file:px-3 file:rounded-xl file:border-0 file:text-xs file:font-semibold file:bg-emerald-50 file:text-emerald-700 hover:file:bg-emerald-100">
        <div id="pay-preview-box" class="mt-2 hidden border rounded-xl p-2 bg-slate-50 text-center max-h-28 overflow-hidden">
          <img id="pay-preview-img" src="" class="max-h-24 mx-auto object-contain rounded">
        </div>
      </div>

      <div class="flex justify-end space-x-2 pt-2">
        <button type="button" onclick="closeRecordPaymentModal()" class="px-4 py-2 border rounded-xl text-xs font-semibold text-slate-600">Cancel</button>
        <button type="button" onclick="saveOrderPayment()" class="px-5 py-2 bg-emerald-600 hover:bg-emerald-700 text-white rounded-xl text-xs font-bold">Confirm Payment</button>
      </div>
    </div>
  </div>

  <!-- MODAL: ORDER RETURN DETAILS -->
  <div id="modal-return-order" class="fixed inset-0 z-[6500] hidden items-center justify-center modal-backdrop p-4">
    <div class="bg-white rounded-3xl max-w-md w-full shadow-2xl border border-slate-100 p-6 space-y-4">
      <div class="flex items-center space-x-2 text-amber-700">
        <i class="fa-solid fa-arrow-rotate-left text-xl"></i>
        <h3 class="text-base font-bold">Process Order Return</h3>
      </div>
      <input type="hidden" id="ret-order-id">

      <div>
        <label class="block text-xs font-bold text-slate-700 mb-1">Return Date</label>
        <input type="date" id="ret-date" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
      </div>
      <div>
        <label class="block text-xs font-bold text-slate-700 mb-1">Given By (Hospital Staff)</label>
        <input type="text" id="ret-given-by" placeholder="e.g. Nurse Supriya / Pharmacy Head" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
      </div>
      <div>
        <label class="block text-xs font-bold text-slate-700 mb-1">Taken By (Stockist Representative)</label>
        <input type="text" id="ret-taken-by" placeholder="e.g. Ramesh Kumar (Rep)" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
      </div>
      <div>
        <label class="block text-xs font-bold text-slate-700 mb-1">Settlement Type</label>
        <select id="ret-settlement-type" onchange="toggleReplacementFields(this.value)" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          <option value="refund">Payment Refund / Credit Note</option>
          <option value="replacement">Goods Replacement Provided</option>
        </select>
      </div>
      <div id="ret-replacement-details-box" class="hidden">
        <label class="block text-xs font-bold text-slate-700 mb-1">Replacement Goods Given</label>
        <textarea id="ret-replacement-notes" rows="2" placeholder="List replacement medicines, batch numbers and quantities given..." class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs"></textarea>
      </div>

      <div class="flex justify-end space-x-2 pt-2">
        <button onclick="closeReturnModal()" class="px-4 py-2 border rounded-xl text-xs font-semibold text-slate-600">Cancel</button>
        <button onclick="confirmOrderReturn()" class="px-5 py-2 bg-amber-600 hover:bg-amber-700 text-white rounded-xl text-xs font-bold">Confirm Return</button>
      </div>
    </div>
  </div>

  <!-- MODAL: UPI SCREENSHOT PREVIEW -->
  <div id="modal-upi-preview" class="fixed inset-0 z-[7000] hidden items-center justify-center modal-backdrop p-4">
    <div class="bg-white rounded-3xl max-w-lg w-full p-6 shadow-2xl border border-slate-100">
      <div class="flex items-center justify-between pb-3 border-b mb-3">
        <h4 class="text-sm font-bold text-slate-800">UPI Payment Proof Screenshot</h4>
        <button onclick="document.getElementById('modal-upi-preview').classList.add('hidden')" class="text-slate-400 hover:text-slate-600">
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>
      <div class="flex items-center justify-center bg-slate-100 rounded-2xl p-2 max-h-[60vh] overflow-hidden">
        <img id="upi-preview-img" src="" alt="UPI Receipt" class="max-h-full max-w-full rounded-xl object-contain">
      </div>
    </div>
  </div>

  <!-- MODAL: ADD MEDICAL REPRESENTATIVE -->
  <div id="modal-add-vendor" class="fixed inset-0 z-[6000] hidden items-center justify-center modal-backdrop p-4 overflow-y-auto">
    <div class="bg-white rounded-3xl max-w-md w-full shadow-2xl border border-slate-100 my-8 overflow-hidden">
      <div class="bg-indigo-700 text-white px-6 py-4 flex items-center justify-between">
        <div class="flex items-center space-x-2">
          <i class="fa-solid fa-user-plus text-lg"></i>
          <h3 class="text-base font-bold" id="vendor-modal-title">Add Rep</h3>
        </div>
        <button onclick="closeAddVendorModal()" class="text-indigo-200 hover:text-white p-1">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>

      <form id="form-vendor" onsubmit="handleSaveVendor(event)" class="p-6 space-y-4">
        <input type="hidden" id="vendor-edit-id">

        <div>
          <label class="block text-xs font-bold text-slate-700 mb-1">Rep Name <span class="text-red-500">*</span></label>
          <input type="text" id="v-name" placeholder="e.g. Ramesh Kumar" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
        </div>

        <div>
          <label class="block text-xs font-bold text-slate-700 mb-1">Mobile No <span class="text-red-500">*</span></label>
          <input type="tel" id="v-mobile" maxlength="10" placeholder="10-digit mobile number" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs font-mono">
        </div>

        <div>
          <label class="block text-xs font-bold text-slate-700 mb-1">Company <span class="text-red-500">*</span></label>
          <input type="text" id="v-company" placeholder="e.g. Sun Pharma / Cipla" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
        </div>

        <div>
          <label class="block text-xs font-bold text-slate-700 mb-1">Products Handled <span class="text-red-500">*</span></label>
          <textarea id="v-products" rows="3" placeholder="e.g. Propofol, Atracurium, Sevoflurane" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs"></textarea>
        </div>

        <div class="flex justify-end space-x-2 pt-2">
          <button type="button" onclick="closeAddVendorModal()" class="px-4 py-2 border rounded-xl text-xs font-semibold text-slate-600">Cancel</button>
          <button type="submit" class="px-5 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded-xl text-xs font-bold">Save Rep</button>
        </div>
      </form>
    </div>
  </div>

  <!-- MODAL: ADD REP SPECIAL OFFER / SCHEME -->
  <div id="modal-add-offer" class="fixed inset-0 z-[6000] hidden items-center justify-center modal-backdrop p-4 overflow-y-auto">
    <div class="bg-white rounded-3xl max-w-md w-full shadow-2xl border border-slate-100 my-8 overflow-hidden">
      <div class="bg-purple-700 text-white px-6 py-4 flex items-center justify-between">
        <div class="flex items-center space-x-2">
          <i class="fa-solid fa-tag text-lg"></i>
          <h3 class="text-base font-bold" id="offer-modal-title">Add Offer Deal</h3>
        </div>
        <button onclick="closeAddOfferModal()" class="text-purple-200 hover:text-white p-1">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>

      <form id="form-offer" onsubmit="handleSaveOffer(event)" class="p-6 space-y-4">
        <input type="hidden" id="offer-edit-id">

        <div>
          <label class="block text-xs font-bold text-slate-700 mb-1">Product Name <span class="text-red-500">*</span></label>
          <input type="text" id="off-product" placeholder="e.g. Cefuroxime 1.5g Inj" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
        </div>

        <div>
          <label class="block text-xs font-bold text-slate-700 mb-1">Company / Rep</label>
          <input type="text" id="off-rep" placeholder="e.g. Cipla / Ramesh Kumar" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
        </div>

        <div class="grid grid-cols-2 gap-3">
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">MRP (₹) <span class="text-red-500">*</span></label>
            <input type="number" step="0.01" id="off-mrp" placeholder="320.00" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          </div>
          <div>
            <label class="block text-xs font-bold text-slate-700 mb-1">Cost Price (C-Price ₹) <span class="text-red-500">*</span></label>
            <input type="number" step="0.01" id="off-cprice" placeholder="180.00" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          </div>
        </div>

        <div>
          <label class="block text-xs font-bold text-slate-700 mb-1">Offer Scheme / Margin <span class="text-red-500">*</span></label>
          <input type="text" id="off-scheme" placeholder="e.g. Buy 10 + 2 Free" required class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
        </div>

        <div class="flex justify-end space-x-2 pt-2">
          <button type="button" onclick="closeAddOfferModal()" class="px-4 py-2 border rounded-xl text-xs font-semibold text-slate-600">Cancel</button>
          <button type="submit" class="px-5 py-2 bg-purple-600 hover:bg-purple-700 text-white rounded-xl text-xs font-bold">Save Deal</button>
        </div>
      </form>
    </div>
  </div>

  <!-- DEDICATED NOTIFICATION CENTER MODAL -->
  <div id="modal-notification-center" class="fixed inset-0 z-[7200] hidden items-center justify-center modal-backdrop p-4 overflow-y-auto">
    <div class="bg-white rounded-3xl max-w-2xl w-full shadow-2xl border border-slate-100 my-8 overflow-hidden transform transition-all">
      
      <!-- Notification Header -->
      <div class="bg-slate-900 text-white px-6 py-4 flex items-center justify-between">
        <div class="flex items-center space-x-3">
          <div class="w-9 h-9 rounded-xl bg-red-500/20 text-red-400 border border-red-500/30 flex items-center justify-center">
            <i class="fa-solid fa-bell text-base"></i>
          </div>
          <div>
            <h3 class="text-base font-bold">Notification Center</h3>
            <p class="text-xs text-slate-400">Purchase orders exceeding 1-month (30 days) waiting period</p>
          </div>
        </div>
        <button onclick="closeNotificationCenter()" class="text-slate-400 hover:text-white p-1">
          <i class="fa-solid fa-xmark text-lg"></i>
        </button>
      </div>

      <!-- Notification Filter Tabs -->
      <div class="border-b border-slate-200 bg-slate-50 px-6 flex space-x-3 text-xs font-bold">
        <button onclick="filterNotificationView('all')" id="notif-tab-all" class="py-3 border-b-2 border-brand-600 text-brand-600 whitespace-nowrap">
          All Alerts (<span id="notif-count-all">0</span>)
        </button>
        <button onclick="filterNotificationView('payments')" id="notif-tab-payments" class="py-3 border-b-2 border-transparent text-slate-600 hover:text-slate-900 whitespace-nowrap">
          Overdue Payments (<span id="notif-count-payments">0</span>)
        </button>
        <button onclick="filterNotificationView('deliveries')" id="notif-tab-deliveries" class="py-3 border-b-2 border-transparent text-slate-600 hover:text-slate-900 whitespace-nowrap">
          Overdue Deliveries (<span id="notif-count-deliveries">0</span>)
        </button>
      </div>

      <!-- Notification Body -->
      <div class="p-6 max-h-[65vh] overflow-y-auto space-y-3.5">
        <div id="notif-empty-state" class="hidden text-center py-10">
          <div class="w-14 h-14 bg-emerald-100 text-emerald-600 rounded-full flex items-center justify-center mx-auto mb-3">
            <i class="fa-solid fa-circle-check text-2xl"></i>
          </div>
          <h4 class="text-sm font-bold text-slate-800">All Payments & Deliveries are Up to Date!</h4>
          <p class="text-xs text-slate-500 mt-1">No purchase orders have exceeded the 1-month (30 days) grace waiting period.</p>
        </div>

        <div id="notif-items-list" class="space-y-3">
          <!-- Dynamically populated notification alert cards -->
        </div>
      </div>

      <!-- Notification Footer -->
      <div class="bg-slate-50 px-6 py-3 border-t border-slate-100 flex items-center justify-between text-xs">
        <span class="text-slate-500"><i class="fa-solid fa-clock-rotate-left mr-1"></i> Threshold: 30 days from PO generation date</span>
        <button onclick="closeNotificationCenter()" class="px-4 py-1.5 bg-slate-200 hover:bg-slate-300 text-slate-700 font-bold rounded-xl transition">
          Close
        </button>
      </div>

    </div>
  </div>

  <!-- PRINT / INVOICE PREVIEW MODAL -->
  <div id="modal-invoice-preview" class="fixed inset-0 z-[7500] hidden items-center justify-center modal-backdrop p-4 overflow-y-auto">
    <div class="bg-white rounded-3xl max-w-4xl w-full shadow-2xl border border-slate-100 my-6 overflow-hidden">
      <!-- Invoice Modal Header Controls (Not Printed) -->
      <div class="bg-slate-900 text-white px-6 py-4 flex items-center justify-between no-print">
        <div class="flex items-center space-x-3">
          <i class="fa-solid fa-file-invoice text-emerald-400 text-lg"></i>
          <div>
            <h3 class="text-sm font-bold" id="invoice-modal-title">Invoice Preview</h3>
            <p class="text-[11px] text-slate-400" id="invoice-modal-mode-label">Mode: With Letterhead</p>
          </div>
        </div>
        <div class="flex items-center space-x-2">
          <button onclick="window.print()" class="px-4 py-2 bg-emerald-600 hover:bg-emerald-700 text-white rounded-xl text-xs font-bold shadow-md transition flex items-center space-x-1.5">
            <i class="fa-solid fa-print"></i>
            <span>Print Invoice</span>
          </button>
          <button onclick="closeInvoiceModal()" class="p-2 text-slate-400 hover:text-white">
            <i class="fa-solid fa-xmark text-lg"></i>
          </button>
        </div>
      </div>

      <!-- Printable Area -->
      <div class="p-8 bg-white" id="printable-bill-area">
        
        <!-- Header Mode 1: With Letterhead -->
        <div id="invoice-header-with" class="border-b-2 border-slate-900 pb-4 mb-6">
          <!-- Text Letterhead Format -->
          <div id="inv-header-text" class="text-center space-y-1">
            <h1 class="text-2xl font-black text-slate-900 tracking-tight" id="inv-hosp-name">AURA MULTISPECIALITY HOSPITAL</h1>
            <p class="text-xs text-slate-600 font-medium" id="inv-hosp-address">123 Health Avenue, Medical Enclave, Bengaluru</p>
            <p class="text-xs text-slate-500 font-medium">
              Phone: <span id="inv-hosp-phone">+91 9876543210</span> | Email/GST: <span id="inv-hosp-email">admin@aurahospital.org</span>
            </p>
            <div class="inline-block mt-1 px-3 py-0.5 bg-slate-900 text-white text-[10px] font-bold uppercase rounded-md tracking-wider">
              Operation Theatre Surgical Pharmacy Dispensing Receipt
            </div>
          </div>

          <!-- Image Letterhead Format -->
          <div id="inv-header-img-container" class="hidden text-center">
            <img id="inv-header-img" src="" alt="Hospital Letterhead" class="max-h-28 mx-auto object-contain">
          </div>
        </div>

        <!-- Header Mode 2: Without Letterhead (Clean Blank Spacing for physical stationery) -->
        <div id="invoice-header-without" class="h-32 mb-6 border-b border-dashed border-slate-300 flex items-center justify-center text-slate-300 text-xs italic no-print hidden">
          [ Letterhead Space Reserved for Pre-Printed Stationery (3.5 inches) ]
        </div>

        <!-- Bill & Patient Metadata Grid -->
        <div class="grid grid-cols-2 gap-4 text-xs mb-6 border border-slate-200 rounded-xl p-4 bg-slate-50/50">
          <div>
            <p><span class="font-bold text-slate-600">Bill No:</span> <span id="inv-bill-no" class="font-mono font-bold text-slate-900">OT-2024-001</span></p>
            <p><span class="font-bold text-slate-600">Patient:</span> <span id="inv-patient-name" class="font-bold text-slate-900">Mr. John Doe</span></p>
            <p><span class="font-bold text-slate-600">Age / Sex:</span> <span id="inv-age-sex">45 Y / Male</span></p>
            <p><span class="font-bold text-slate-600">Mobile:</span> <span id="inv-mobile" class="font-mono">9876543210</span></p>
          </div>
          <div>
            <p><span class="font-bold text-slate-600">Billing Date:</span> <span id="inv-date" class="font-bold text-slate-900">01/01/2025</span></p>
            <p><span class="font-bold text-slate-600">IP No / UHID:</span> <span id="inv-ip-uhid" class="font-mono">IP-1029 / UH-8821</span></p>
            <p><span class="font-bold text-slate-600">Doctor / Surgeon:</span> <span id="inv-doctor" class="font-bold text-slate-900">Dr. Sharma</span></p>
            <p><span class="font-bold text-slate-600">Surgery / Proc:</span> <span id="inv-surgery">Laparoscopic Cholecystectomy</span></p>
          </div>
        </div>

        <!-- Medicines Table on Bill -->
        <table class="w-full text-left text-xs border-collapse mb-6">
          <thead>
            <tr class="border-y-2 border-slate-800 text-[11px] font-bold uppercase text-slate-800">
              <th class="py-2 px-2">#</th>
              <th class="py-2 px-2">Medicine Item Description</th>
              <th class="py-2 px-2">Batch</th>
              <th class="py-2 px-2 text-right">MRP (₹)</th>
              <th class="py-2 px-2 text-center">Qty</th>
              <th class="py-2 px-2 text-right">Amount (₹)</th>
            </tr>
          </thead>
          <tbody id="inv-items-tbody" class="divide-y divide-slate-200">
            <!-- Items injected -->
          </tbody>
        </table>

        <!-- Total and In Words -->
        <div class="border-t-2 border-slate-900 pt-3 flex flex-col sm:flex-row items-center justify-between gap-4 mb-12">
          <div>
            <p class="text-[11px] font-bold uppercase text-slate-600">Amount in Words:</p>
            <p class="text-xs font-bold text-slate-900 italic mt-0.5" id="inv-total-words">Rupees Four Thousand Two Hundred Only</p>
          </div>
          <div class="text-right">
            <p class="text-[11px] font-bold uppercase text-slate-600">Grand Total:</p>
            <h2 class="text-2xl font-black text-slate-900" id="inv-total-num">₹ 4,200.00</h2>
          </div>
        </div>

        <!-- Signature Section -->
        <div class="grid grid-cols-2 gap-8 pt-10 text-xs text-center">
          <div>
            <div class="border-t border-slate-400 w-48 mx-auto mb-1"></div>
            <p class="font-semibold text-slate-700">OT Nurse / Pharmacist</p>
          </div>
          <div>
            <div class="border-t border-slate-400 w-48 mx-auto mb-1"></div>
            <p class="font-semibold text-slate-700">Authorized Signature</p>
          </div>
        </div>

      </div>
    </div>
  </div>

  <script>
    // ==========================================
    // INITIAL SAMPLE DATA & STORAGE SCHEMAS
    // ==========================================
    const DEFAULT_CREDENTIALS = {
      userId: 'admin',
      password: 'admin123',
      masterKey: 'MASTER@OVERRIDE'
    };

    const DEFAULT_SETTINGS = {
      portalName: 'AURA MULTISPECIALITY HOSPITAL',
      portalSubtitle: 'Operation Theatre Pharmacy & Surgical Inventory',
      letterheadMode: 'text', // 'text' or 'image'
      hospitalName: 'AURA MULTISPECIALITY HOSPITAL',
      hospitalAddress: '123 Health Avenue, Medical Enclave, Bengaluru, Karnataka - 560001',
      hospitalPhone: '+91 9876543210 / 080-2345678',
      hospitalEmail: 'ot.pharmacy@aurahospital.org / GSTIN: 29AABCU9603R1ZM',
      letterheadLogo: ''
    };

    const DEFAULT_DOCTORS = [
      'Dr. Rajesh Sharma, MS (Gen Surgery)',
      'Dr. Anita Rao, MD (Anaesthesiology)',
      'Dr. Vikram Patel, MCh (Surgical Gastro)',
      'Dr. Priya Deshmukh, MS (OBG)',
      'Dr. Arun Kulkarni, MS (Orthopaedics)'
    ];

    const DEFAULT_STOCKISTS = [
      'Apollo Medical Supplies Ltd',
      'MediSource Healthcare Logistics',
      'LifeCare Surgical Pharma',
      'Karnataka Drug House',
      'BioGen Surgo-Pharma Corp'
    ];

    const DEFAULT_MEDICINES = [
      { id: 'med-1', name: 'Propofol 10mg/ml 20ml Inj', stockist: 'Apollo Medical Supplies Ltd', batch: 'PF-9081', mfr: '08/2024 - 07/2026', qty: 45, mrp: 280.00 },
      { id: 'med-2', name: 'Atracurium Besylate 25mg/2.5ml', stockist: 'MediSource Healthcare Logistics', batch: 'AT-3342', mfr: '09/2024 - 08/2026', qty: 30, mrp: 145.00 },
      { id: 'med-3', name: 'Sevoflurane Inhalation 250ml', stockist: 'LifeCare Surgical Pharma', batch: 'SV-1092', mfr: '05/2024 - 04/2027', qty: 8, mrp: 4850.00 },
      { id: 'med-4', name: 'Cefuroxime 1.5g Injection', stockist: 'Apollo Medical Supplies Ltd', batch: 'CF-4451', mfr: '10/2024 - 09/2026', qty: 60, mrp: 210.00 },
      { id: 'med-5', name: 'Fentanyl Citrate 50mcg/ml 2ml', stockist: 'Karnataka Drug House', batch: 'FN-8812', mfr: '07/2024 - 06/2026', qty: 25, mrp: 95.00 },
      { id: 'med-6', name: 'Vicryl 2-0 Suture NW2664', stockist: 'BioGen Surgo-Pharma Corp', batch: 'VC-0911', mfr: '01/2024 - 12/2028', qty: 120, mrp: 420.00 }
    ];

    const DEFAULT_TEMPLATES = [
      {
        id: 'tpl-1',
        name: 'Laparoscopic Cholecystectomy Kit',
        department: 'General & GI Surgery',
        items: [
          { name: 'Propofol 10mg/ml 20ml Inj', batch: 'PF-9081', qty: 2, mrp: 280.00 },
          { name: 'Atracurium Besylate 25mg/2.5ml', batch: 'AT-3342', qty: 2, mrp: 145.00 },
          { name: 'Cefuroxime 1.5g Injection', batch: 'CF-4451', qty: 1, mrp: 210.00 },
          { name: 'Vicryl 2-0 Suture NW2664', batch: 'VC-0911', qty: 2, mrp: 420.00 }
        ]
      },
      {
        id: 'tpl-2',
        name: 'Hernia Repair Surgical Kit',
        department: 'General Surgery',
        items: [
          { name: 'Propofol 10mg/ml 20ml Inj', batch: 'PF-9081', qty: 1, mrp: 280.00 },
          { name: 'Cefuroxime 1.5g Injection', batch: 'CF-4451', qty: 1, mrp: 210.00 },
          { name: 'Vicryl 2-0 Suture NW2664', batch: 'VC-0911', qty: 1, mrp: 420.00 }
        ]
      }
    ];

    const DEFAULT_VENDORS = [
      {
        id: 'ven-1',
        name: 'Suresh Bhatia',
        mobile: '9845012345',
        company: 'Sun Pharma Surgical Division',
        products: 'Propofol, Sevoflurane, Midazolam, Ketamine'
      },
      {
        id: 'ven-2',
        name: 'Rameshwar Reddy',
        mobile: '9980123456',
        company: 'Cipla Critical Care',
        products: 'Cefuroxime, Piperacillin Tazobactam, Ondansetron, Tramadol'
      },
      {
        id: 'ven-3',
        name: 'Kavitha Sundaram',
        mobile: '9880198765',
        company: 'Ethicon Johnson & Johnson',
        products: 'Vicryl Sutures, Prolene Mesh, Ethilon, Surgicel'
      }
    ];

    const DEFAULT_OFFERS = [
      {
        id: 'off-1',
        product: 'Propofol 10mg/ml 20ml Inj',
        rep: 'Suresh Bhatia (Sun Pharma)',
        mrp: 280.00,
        cprice: 165.00,
        scheme: 'Buy 50 Vials + 10 Free (20% Extra Margin)'
      },
      {
        id: 'off-2',
        product: 'Cefuroxime 1.5g Inj',
        rep: 'Rameshwar Reddy (Cipla)',
        mrp: 210.00,
        cprice: 110.00,
        scheme: 'Special OT Bulk Scheme: 10 + 2 Free Pack'
      }
    ];

    // LocalStorage helper wrappers
    function getStore(key, defaultVal) {
      try {
        const val = localStorage.getItem('mediflow_' + key);
        return val ? JSON.parse(val) : defaultVal;
      } catch (e) {
        return defaultVal;
      }
    }

    function setStore(key, val) {
      try {
        localStorage.setItem('mediflow_' + key, JSON.stringify(val));
      } catch (e) {
        console.error('Storage write error', e);
      }
    }

    // App state
    let credentials = getStore('credentials', DEFAULT_CREDENTIALS);
    let appSettings = getStore('settings', DEFAULT_SETTINGS);
    let doctorsList = getStore('doctors', DEFAULT_DOCTORS);
    let stockistsList = getStore('stockists', DEFAULT_STOCKISTS);
    let medicines = getStore('medicines', DEFAULT_MEDICINES);
    let billsArchive = getStore('bills', []);
    let purchaseOrders = getStore('purchase_orders', []);
    let templates = getStore('templates', DEFAULT_TEMPLATES);
    let vendors = getStore('vendors', DEFAULT_VENDORS);
    let repOffers = getStore('offers', DEFAULT_OFFERS);

    // Dynamic session state
    let isAuthenticated = false;
    let isMasterOverride = false;
    let currentSellItems = [];
    let currentTemplateDraftItems = [];
    let currentOrderDraftItems = [];
    let isArchiveVisible = false;
    let paymentScreenshotDraft = null;
    let currentNotifFilter = 'all';

    function showToast(message, type = 'success') {
      const container = document.getElementById('toast-container');
      const toast = document.createElement('div');
      
      const colors = {
        success: 'bg-emerald-600 text-white shadow-emerald-500/20',
        error: 'bg-red-600 text-white shadow-red-500/20',
        warning: 'bg-amber-500 text-white shadow-amber-500/20',
        info: 'bg-brand-600 text-white shadow-brand-500/20'
      };

      const icons = {
        success: 'fa-circle-check',
        error: 'fa-circle-xmark',
        warning: 'fa-triangle-exclamation',
        info: 'fa-circle-info'
      };

      toast.className = `pointer-events-auto flex items-center space-x-2.5 px-4 py-3 rounded-2xl shadow-xl text-xs font-semibold ${colors[type] || colors.info} transform transition-all duration-300 translate-y-2 opacity-0`;
      toast.innerHTML = `<i class="fa-solid ${icons[type] || icons.info} text-sm"></i> <span>${message}</span>`;
      
      container.appendChild(toast);
      requestAnimationFrame(() => {
        toast.classList.remove('translate-y-2', 'opacity-0');
      });

      setTimeout(() => {
        toast.classList.add('opacity-0', 'scale-95');
        setTimeout(() => toast.remove(), 300);
      }, 3500);
    }

    function showConfirmModal(title, message, onConfirm) {
      const modal = document.getElementById('confirm-modal');
      document.getElementById('confirm-title').innerText = title;
      document.getElementById('confirm-message').innerText = message;
      modal.classList.remove('hidden');
      modal.classList.add('flex');

      const btnCancel = document.getElementById('confirm-btn-cancel');
      const btnOk = document.getElementById('confirm-btn-ok');

      function cleanup() {
        modal.classList.add('hidden');
        modal.classList.remove('flex');
        btnCancel.removeEventListener('click', onCancelClick);
        btnOk.removeEventListener('click', onOkClick);
      }

      function onCancelClick() {
        cleanup();
      }

      function onOkClick() {
        cleanup();
        if (typeof onConfirm === 'function') onConfirm();
      }

      btnCancel.addEventListener('click', onCancelClick);
      btnOk.addEventListener('click', onOkClick);
    }

    // Number to words converter (Indian / Standard medical billing formatting)
    function numberToWords(num) {
      if (num === null || num === undefined || isNaN(num) || num <= 0) return 'Zero Rupees Only';
      
      const a = ['', 'One ', 'Two ', 'Three ', 'Four ', 'Five ', 'Six ', 'Seven ', 'Eight ', 'Nine ', 'Ten ', 'Eleven ', 'Twelve ', 'Thirteen ', 'Fourteen ', 'Fifteen ', 'Sixteen ', 'Seventeen ', 'Eighteen ', 'Nineteen '];
      const b = ['', '', 'Twenty', 'Thirty', 'Forty', 'Fifty', 'Sixty', 'Seventy', 'Eighty', 'Ninety'];

      function inWords(n) {
        if ((n = n.toString()).length > 9) return 'overflow';
        let n_str = ('000000000' + n).substr(-9);
        let n_arr = n_str.match(/^(\d{2})(\d{2})(\d{2})(\d{1})(\d{2})$/);
        if (!n_arr) return '';
        let str = '';
        str += (n_arr[1] != 0) ? (a[Number(n_arr[1])] || b[n_arr[1][0]] + ' ' + a[n_arr[1][1]]) + 'Crore ' : '';
        str += (n_arr[2] != 0) ? (a[Number(n_arr[2])] || b[n_arr[2][0]] + ' ' + a[n_arr[2][1]]) + 'Lakh ' : '';
        str += (n_arr[3] != 0) ? (a[Number(n_arr[3])] || b[n_arr[3][0]] + ' ' + a[n_arr[3][1]]) + 'Thousand ' : '';
        str += (n_arr[4] != 0) ? (a[Number(n_arr[4])] || b[n_arr[4][0]] + ' ' + a[n_arr[4][1]]) + 'Hundred ' : '';
        str += (n_arr[5] != 0) ? ((str != '') ? 'and ' : '') + (a[Number(n_arr[5])] || b[n_arr[5][0]] + ' ' + a[n_arr[5][1]]) : '';
        return str;
      }

      const parts = num.toFixed(2).split('.');
      const rupees = inWords(parseInt(parts[0]));
      const paise = parseInt(parts[1]) > 0 ? ' and ' + inWords(parseInt(parts[1])) + 'Paise' : '';
      return (rupees.trim() ? 'Rupees ' + rupees : '') + paise + ' Only';
    }

    const authDoorway = document.getElementById('auth-doorway');
    const loginForm = document.getElementById('login-form');
    const toggleOverrideBtn = document.getElementById('toggle-override-btn');
    const standardAuthFields = document.getElementById('standard-auth-fields');
    const masterAuthField = document.getElementById('master-auth-field');
    const overrideTextLabel = document.getElementById('override-text-label');

    toggleOverrideBtn.addEventListener('click', () => {
      isMasterOverride = !isMasterOverride;
      if (isMasterOverride) {
        standardAuthFields.classList.add('hidden');
        masterAuthField.classList.remove('hidden');
        overrideTextLabel.innerText = 'Back to Standard User ID & Password';
        document.getElementById('login-userid').removeAttribute('required');
        document.getElementById('login-password').removeAttribute('required');
        document.getElementById('login-master-key').setAttribute('required', 'true');
        document.getElementById('login-master-key').focus();
      } else {
        standardAuthFields.classList.remove('hidden');
        masterAuthField.classList.add('hidden');
        overrideTextLabel.innerText = 'Override with Master Password';
        document.getElementById('login-userid').setAttribute('required', 'true');
        document.getElementById('login-password').setAttribute('required', 'true');
        document.getElementById('login-master-key').removeAttribute('required');
      }
    });

    loginForm.addEventListener('submit', (e) => {
      e.preventDefault();
      if (isMasterOverride) {
        const masterKeyInput = document.getElementById('login-master-key').value.trim();
        if (masterKeyInput === credentials.masterKey) {
          grantAccess('Master Override Authorized');
        } else {
          showToast('Invalid Master Override Key!', 'error');
        }
      } else {
        const u = document.getElementById('login-userid').value.trim();
        const p = document.getElementById('login-password').value.trim();
        if (u === credentials.userId && p === credentials.password) {
          grantAccess(`Welcome back, ${u}`);
        } else {
          showToast('Invalid User ID or Password!', 'error');
        }
      }
    });

    function grantAccess(msg) {
      isAuthenticated = true;
      authDoorway.classList.add('opacity-0', 'pointer-events-none', 'transition-opacity', 'duration-500');
      setTimeout(() => {
        authDoorway.style.display = 'none';
      }, 500);
      showToast(msg, 'success');
      refreshAllViews();
    }

    function lockDoorway() {
      isAuthenticated = false;
      authDoorway.style.display = 'flex';
      requestAnimationFrame(() => {
        authDoorway.classList.remove('opacity-0', 'pointer-events-none');
      });
      document.getElementById('login-password').value = '';
      document.getElementById('login-master-key').value = '';
      showToast('Portal door locked securely.', 'info');
    }

    function switchMainTab(tab) {
      const tabs = ['ot', 'purchase', 'vendors'];
      tabs.forEach(t => {
        const section = document.getElementById(`module-${t}`);
        const navBtn = document.getElementById(`nav-btn-${t}`);
        const mobBtn = document.getElementById(`mob-nav-${t}`);

        if (t === tab) {
          section.classList.remove('hidden');
          if (navBtn) {
            navBtn.className = 'px-5 py-2 text-sm font-semibold rounded-xl transition flex items-center space-x-2 bg-white text-brand-700 shadow-sm';
          }
          if (mobBtn) {
            mobBtn.className = 'px-3 py-1.5 text-xs font-semibold rounded-lg bg-brand-600 text-white whitespace-nowrap';
          }
        } else {
          section.classList.add('hidden');
          if (navBtn) {
            navBtn.className = 'px-5 py-2 text-sm font-semibold rounded-xl transition flex items-center space-x-2 text-slate-600 hover:text-slate-900';
          }
          if (mobBtn) {
            mobBtn.className = 'px-3 py-1.5 text-xs font-semibold rounded-lg text-slate-600 hover:bg-slate-100 whitespace-nowrap';
          }
        }
      });
      
      if (tab === 'purchase') renderPurchaseOrders();
      if (tab === 'vendors') renderVendorsAndOffers();
      if (tab === 'ot') {
        renderOTInventoryTable();
        renderBillsArchive();
      }
    }

    function openSettingsModal() {
      document.getElementById('modal-settings').classList.remove('hidden');
      document.getElementById('modal-settings').classList.add('flex');
      loadSettingsIntoForm();
      renderDoctorsListTable();
      renderStockistsListTable();
    }

    function closeSettingsModal() {
      document.getElementById('modal-settings').classList.add('hidden');
      document.getElementById('modal-settings').classList.remove('flex');
    }

    function switchSettingsTab(tabName) {
      const tabs = ['letterhead', 'doctors', 'stockists', 'credentials', 'appname'];
      tabs.forEach(t => {
        const btn = document.getElementById(`stab-${t}`);
        const pane = document.getElementById(`spane-${t}`);
        if (t === tabName) {
          btn.className = 'py-3 border-b-2 border-brand-600 text-brand-600 whitespace-nowrap font-bold';
          pane.classList.remove('hidden');
        } else {
          btn.className = 'py-3 border-b-2 border-transparent text-slate-600 hover:text-slate-900 whitespace-nowrap';
          pane.classList.add('hidden');
        }
      });
    }

    function loadSettingsIntoForm() {
      document.getElementById('set-letterhead-mode').value = appSettings.letterheadMode || 'text';
      document.getElementById('set-hosp-name').value = appSettings.hospitalName || '';
      document.getElementById('set-hosp-address').value = appSettings.hospitalAddress || '';
      document.getElementById('set-hosp-phone').value = appSettings.hospitalPhone || '';
      document.getElementById('set-hosp-email').value = appSettings.hospitalEmail || '';
      
      const preview = document.getElementById('letterhead-image-preview');
      if (appSettings.letterheadLogo) {
        preview.innerHTML = `<img src="${appSettings.letterheadLogo}" class="max-h-24 mx-auto object-contain">`;
      } else {
        preview.innerHTML = `<span class="text-xs text-slate-400">No image uploaded</span>`;
      }

      document.getElementById('set-cred-userid').value = credentials.userId;
      document.getElementById('set-cred-pass').value = credentials.password;
      document.getElementById('set-cred-master').value = credentials.masterKey;

      document.getElementById('set-portal-title').value = appSettings.portalName || '';
      document.getElementById('set-portal-subtitle').value = appSettings.portalSubtitle || '';
    }

    // Handle logo file input converting to dataURL
    document.getElementById('set-hosp-logo-file').addEventListener('change', function(e) {
      const file = e.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = function(evt) {
          appSettings.letterheadLogo = evt.target.result;
          document.getElementById('letterhead-image-preview').innerHTML = `<img src="${evt.target.result}" class="max-h-24 mx-auto object-contain">`;
        };
        reader.readAsDataURL(file);
      }
    });

    function saveLetterheadSettings() {
      appSettings.letterheadMode = document.getElementById('set-letterhead-mode').value;
      appSettings.hospitalName = document.getElementById('set-hosp-name').value.trim();
      appSettings.hospitalAddress = document.getElementById('set-hosp-address').value.trim();
      appSettings.hospitalPhone = document.getElementById('set-hosp-phone').value.trim();
      appSettings.hospitalEmail = document.getElementById('set-hosp-email').value.trim();
      setStore('settings', appSettings);
      updateAppBrandingDisplay();
      showToast('Hospital letterhead settings saved!', 'success');
    }

    function saveCredentialsSettings() {
      const u = document.getElementById('set-cred-userid').value.trim();
      const p = document.getElementById('set-cred-pass').value.trim();
      const m = document.getElementById('set-cred-master').value.trim();
      if (!u || !p || !m) {
        showToast('All credential fields are required!', 'error');
        return;
      }
      credentials = { userId: u, password: p, masterKey: m };
      setStore('credentials', credentials);
      showToast('User credentials and master key updated successfully!', 'success');
    }

    function savePortalNameSettings() {
      appSettings.portalName = document.getElementById('set-portal-title').value.trim() || 'AURA MULTISPECIALITY HOSPITAL';
      appSettings.portalSubtitle = document.getElementById('set-portal-subtitle').value.trim() || 'Operation Theatre Pharmacy';
      setStore('settings', appSettings);
      updateAppBrandingDisplay();
      showToast('Portal branding updated!', 'success');
    }

    function updateAppBrandingDisplay() {
      document.getElementById('doorway-app-name').innerText = appSettings.portalName;
      document.getElementById('topbar-hospital-name').innerText = appSettings.portalName;
      document.getElementById('topbar-site-subname').innerText = appSettings.portalSubtitle;
    }

    // Doctors CRUD
    function renderDoctorsListTable() {
      const tbody = document.getElementById('doctors-list-tbody');
      tbody.innerHTML = '';
      doctorsList.forEach((doc, idx) => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td class="p-3 font-semibold text-slate-800">${doc}</td>
          <td class="p-3 text-right">
            <button onclick="deleteDoctor(${idx})" class="text-red-500 hover:text-red-700 p-1"><i class="fa-solid fa-trash-can"></i></button>
          </td>
        `;
        tbody.appendChild(tr);
      });
      populateDoctorDropdowns();
    }

    function addDoctor() {
      const input = document.getElementById('new-doctor-name');
      const name = input.value.trim();
      if (!name) return showToast('Please enter doctor name', 'warning');
      doctorsList.push(name);
      setStore('doctors', doctorsList);
      input.value = '';
      renderDoctorsListTable();
      showToast('Doctor added to hospital directory', 'success');
    }

    function deleteDoctor(idx) {
      doctorsList.splice(idx, 1);
      setStore('doctors', doctorsList);
      renderDoctorsListTable();
      showToast('Doctor removed', 'info');
    }

    // Stockists CRUD
    function renderStockistsListTable() {
      const tbody = document.getElementById('stockists-list-tbody');
      tbody.innerHTML = '';
      stockistsList.forEach((stk, idx) => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td class="p-3 font-semibold text-slate-800">${stk}</td>
          <td class="p-3 text-right">
            <button onclick="deleteStockist(${idx})" class="text-red-500 hover:text-red-700 p-1"><i class="fa-solid fa-trash-can"></i></button>
          </td>
        `;
        tbody.appendChild(tr);
      });
      populateStockistDropdowns();
    }

    function addStockist() {
      const input = document.getElementById('new-stockist-name');
      const name = input.value.trim();
      if (!name) return showToast('Please enter stockist name', 'warning');
      stockistsList.push(name);
      setStore('stockists', stockistsList);
      input.value = '';
      renderStockistsListTable();
      showToast('Stockist added', 'success');
    }

    function deleteStockist(idx) {
      stockistsList.splice(idx, 1);
      setStore('stockists', stockistsList);
      renderStockistsListTable();
      showToast('Stockist removed', 'info');
    }

    function populateDoctorDropdowns() {
      const select = document.getElementById('bill-doctor');
      if (!select) return;
      select.innerHTML = '';
      doctorsList.forEach(d => {
        const opt = document.createElement('option');
        opt.value = d;
        opt.textContent = d;
        select.appendChild(opt);
      });
    }

    function populateStockistDropdowns() {
      const selects = [document.getElementById('med-stockist'), document.getElementById('po-stockist')];
      selects.forEach(sel => {
        if (!sel) return;
        sel.innerHTML = '';
        stockistsList.forEach(s => {
          const opt = document.createElement('option');
          opt.value = s;
          opt.textContent = s;
          sel.appendChild(opt);
        });
      });
    }

    function renderOTInventoryTable() {
      const query = (document.getElementById('ot-inventory-search').value || '').toLowerCase().trim();
      const tbody = document.getElementById('ot-inventory-tbody');
      tbody.innerHTML = '';

      const filtered = medicines.filter(m => {
        return m.name.toLowerCase().includes(query) ||
               m.stockist.toLowerCase().includes(query) ||
               m.batch.toLowerCase().includes(query);
      });

      // Update stat cards
      document.getElementById('stat-ot-total-items').innerText = medicines.length;
      document.getElementById('stat-ot-low-stock').innerText = medicines.filter(m => Number(m.qty) < 10).length;
      document.getElementById('stat-ot-total-templates').innerText = templates.length;

      if (filtered.length === 0) {
        tbody.innerHTML = `<tr><td colspan="8" class="text-center py-8 text-slate-400">No medicine items found. Click '1. ADD MEDICINE' above.</td></tr>`;
        return;
      }

      filtered.forEach((med, i) => {
        const isLow = Number(med.qty) < 10;
        const tr = document.createElement('tr');
        tr.className = 'hover:bg-slate-50/80 transition';
        tr.innerHTML = `
          <td class="py-3 px-4 font-mono text-slate-400">${i + 1}</td>
          <td class="py-3 px-4">
            <span class="font-bold text-slate-900">${med.name}</span>
          </td>
          <td class="py-3 px-4 text-slate-600">${med.stockist}</td>
          <td class="py-3 px-4 font-mono text-slate-800 font-semibold">${med.batch}</td>
          <td class="py-3 px-4 text-slate-500">${med.mfr}</td>
          <td class="py-3 px-4 text-center">
            <span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-bold ${isLow ? 'bg-red-100 text-red-700 animate-pulse' : 'bg-emerald-100 text-emerald-800'}">
              ${med.qty} units
            </span>
          </td>
          <td class="py-3 px-4 text-right font-bold text-slate-900">₹ ${Number(med.mrp).toFixed(2)}</td>
          <td class="py-3 px-4 text-center space-x-1.5 whitespace-nowrap">
            <button onclick="editOTMedicine('${med.id}')" class="p-1.5 text-brand-600 hover:text-brand-800 hover:bg-brand-50 rounded-lg transition" title="Edit Medicine">
              <i class="fa-solid fa-pen-to-square"></i>
            </button>
            <button onclick="deleteOTMedicine('${med.id}')" class="p-1.5 text-red-500 hover:text-red-700 hover:bg-red-50 rounded-lg transition" title="Delete Medicine">
              <i class="fa-solid fa-trash-can"></i>
            </button>
          </td>
        `;
        tbody.appendChild(tr);
      });
    }

    function openAddMedicineModal() {
      populateStockistDropdowns();
      document.getElementById('form-add-medicine').reset();
      document.getElementById('med-edit-id').value = '';
      document.getElementById('add-med-modal-title').innerText = 'Add OT Medicine';
      document.getElementById('btn-save-med-text').innerText = 'Add & Keep In Page';
      document.getElementById('modal-add-medicine').classList.remove('hidden');
      document.getElementById('modal-add-medicine').classList.add('flex');
    }

    function closeAddMedicineModal() {
      document.getElementById('modal-add-medicine').classList.add('hidden');
      document.getElementById('modal-add-medicine').classList.remove('flex');
    }

    function handleSaveMedicine(e) {
      e.preventDefault();
      const editId = document.getElementById('med-edit-id').value;
      const name = document.getElementById('med-name').value.trim();
      const stockist = document.getElementById('med-stockist').value;
      const batch = document.getElementById('med-batch').value.trim();
      const mfr = document.getElementById('med-mfr').value.trim();
      const qty = parseInt(document.getElementById('med-qty').value, 10);
      const mrp = parseFloat(document.getElementById('med-mrp').value);

      if (editId) {
        // Edit existing
        const idx = medicines.findIndex(m => m.id === editId);
        if (idx !== -1) {
          medicines[idx] = { ...medicines[idx], name, stockist, batch, mfr, qty, mrp };
          showToast('Medicine updated successfully!', 'success');
        }
      } else {
        // Create new
        const newMed = {
          id: 'med-' + Date.now(),
          name,
          stockist,
          batch,
          mfr,
          qty,
          mrp
        };
        medicines.unshift(newMed);
        showToast('Medicine added to OT stock! Form kept open.', 'success');
        // Clear inputs for rapid adding while keeping page/modal open
        document.getElementById('med-name').value = '';
        document.getElementById('med-batch').value = '';
        document.getElementById('med-qty').value = '';
        document.getElementById('med-name').focus();
      }

      setStore('medicines', medicines);
      renderOTInventoryTable();
    }

    function editOTMedicine(id) {
      const med = medicines.find(m => m.id === id);
      if (!med) return;
      openAddMedicineModal();
      document.getElementById('add-med-modal-title').innerText = 'Edit OT Medicine';
      document.getElementById('btn-save-med-text').innerText = 'Update Medicine';
      document.getElementById('med-edit-id').value = med.id;
      document.getElementById('med-name').value = med.name;
      document.getElementById('med-stockist').value = med.stockist;
      document.getElementById('med-batch').value = med.batch;
      document.getElementById('med-mfr').value = med.mfr;
      document.getElementById('med-qty').value = med.qty;
      document.getElementById('med-mrp').value = med.mrp;
    }

    function deleteOTMedicine(id) {
      showConfirmModal('Delete OT Medicine', 'Are you sure you want to delete this medicine from inventory?', () => {
        medicines = medicines.filter(m => m.id !== id);
        setStore('medicines', medicines);
        renderOTInventoryTable();
        showToast('Medicine deleted', 'info');
      });
    }

    function exportOTInventoryToExcel() {
      let csvContent = 'data:text/csv;charset=utf-8,ID,Medicine Name,Stockist,Batch No,MFR / Expiry,Stock Qty,MRP\n';
      medicines.forEach(m => {
        csvContent += `"${m.id}","${m.name}","${m.stockist}","${m.batch}","${m.mfr}",${m.qty},${m.mrp}\n`;
      });
      const encodedUri = encodeURI(csvContent);
      const link = document.createElement('a');
      link.setAttribute('href', encodedUri);
      link.setAttribute('download', `OT_Inventory_${new Date().toISOString().slice(0, 10)}.csv`);
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }

    function openSellModal() {
      populateDoctorDropdowns();
      currentSellItems = [];
      document.getElementById('bill-patient-name').value = '';
      document.getElementById('bill-age').value = '';
      document.getElementById('bill-mobile').value = '';
      document.getElementById('bill-ip').value = '';
      document.getElementById('bill-uhid').value = '';
      document.getElementById('bill-surgery').value = '';
      document.getElementById('bill-date').value = new Date().toISOString().slice(0, 10);
      document.getElementById('sell-med-search').value = '';
      document.getElementById('sell-search-results').classList.add('hidden');
      renderBillItemsTable();
      document.getElementById('modal-sell').classList.remove('hidden');
      document.getElementById('modal-sell').classList.add('flex');
    }

    function closeSellModal() {
      document.getElementById('modal-sell').classList.add('hidden');
      document.getElementById('modal-sell').classList.remove('flex');
    }

    // Search and Click engine (NOT dropdown)
    function handleSellMedSearch(val) {
      const resultsBox = document.getElementById('sell-search-results');
      const query = val.toLowerCase().trim();
      if (!query) {
        resultsBox.classList.add('hidden');
        resultsBox.innerHTML = '';
        return;
      }

      const matches = medicines.filter(m => m.name.toLowerCase().includes(query) || m.batch.toLowerCase().includes(query));
      if (matches.length === 0) {
        resultsBox.classList.remove('hidden');
        resultsBox.innerHTML = `<div class="p-3 text-xs text-slate-400 text-center">No medicines in stock matching "${val}".</div>`;
        return;
      }

      resultsBox.classList.remove('hidden');
      resultsBox.innerHTML = '';
      matches.forEach(item => {
        const itemDiv = document.createElement('div');
        itemDiv.className = 'p-2.5 hover:bg-brand-50 cursor-pointer flex items-center justify-between transition';
        itemDiv.innerHTML = `
          <div>
            <p class="font-bold text-slate-800 text-xs">${item.name}</p>
            <p class="text-[11px] text-slate-500">Batch: <span class="font-mono">${item.batch}</span> | MRP: ₹${item.mrp.toFixed(2)} | Avail Stock: <span class="font-bold ${item.qty <= 0 ? 'text-red-500' : 'text-emerald-600'}">${item.qty} units</span></p>
          </div>
          <button type="button" class="px-3 py-1 bg-brand-600 hover:bg-brand-700 text-white rounded-lg text-xs font-semibold">
            + Add
          </button>
        `;
        itemDiv.onclick = () => {
          addMedicineToSellBill(item);
          resultsBox.classList.add('hidden');
          document.getElementById('sell-med-search').value = '';
        };
        resultsBox.appendChild(itemDiv);
      });
    }

    function addMedicineToSellBill(med) {
      if (med.qty <= 0) {
        showToast(`'${med.name}' is Out of Stock!`, 'error');
        return;
      }

      const existing = currentSellItems.find(i => i.medId === med.id);
      if (existing) {
        if (existing.qty + 1 > med.qty) {
          showToast(`Cannot exceed live available stock (${med.qty} units)!`, 'warning');
          return;
        }
        existing.qty += 1;
        existing.subtotal = existing.qty * existing.mrp;
      } else {
        currentSellItems.push({
          medId: med.id,
          name: med.name,
          batch: med.batch,
          mrp: Number(med.mrp),
          qty: 1,
          maxStock: med.qty,
          subtotal: Number(med.mrp)
        });
      }
      renderBillItemsTable();
      showToast(`Added ${med.name}`, 'info');
    }

    function updateSellItemQty(index, newQty) {
      const qty = parseInt(newQty, 10);
      const item = currentSellItems[index];
      if (!item) return;

      if (isNaN(qty) || qty <= 0) {
        removeSellItem(index);
        return;
      }

      if (qty > item.maxStock) {
        showToast(`Stock limit reached! Available: ${item.maxStock}`, 'warning');
        item.qty = item.maxStock;
      } else {
        item.qty = qty;
      }
      item.subtotal = item.qty * item.mrp;
      renderBillItemsTable();
    }

    function updateSellItemMRP(index, newMrp) {
      const val = parseFloat(newMrp);
      const item = currentSellItems[index];
      if (!item || isNaN(val) || val < 0) return;
      item.mrp = val;
      item.subtotal = item.qty * item.mrp;
      renderBillItemsTable();
    }

    function removeSellItem(index) {
      currentSellItems.splice(index, 1);
      renderBillItemsTable();
    }

    function renderBillItemsTable() {
      const tbody = document.getElementById('bill-items-tbody');
      tbody.innerHTML = '';
      let grandTotal = 0;

      if (currentSellItems.length === 0) {
        tbody.innerHTML = `<tr><td colspan="6" class="p-6 text-center text-slate-400">No medicines added yet. Use the search bar above or load a template.</td></tr>`;
        document.getElementById('bill-items-count').innerText = '0 items';
        document.getElementById('bill-total-amount').innerText = '₹ 0.00';
        document.getElementById('bill-amount-words').innerText = 'Zero Rupees Only';
        return;
      }

      currentSellItems.forEach((item, idx) => {
        grandTotal += item.subtotal;
        const tr = document.createElement('tr');
        tr.className = 'hover:bg-slate-50';
        tr.innerHTML = `
          <td class="p-3 font-bold text-slate-800">${item.name}</td>
          <td class="p-3 font-mono text-slate-600">${item.batch}</td>
          <td class="p-3 text-right">
            <input type="number" step="0.01" value="${item.mrp.toFixed(2)}" onchange="updateSellItemMRP(${idx}, this.value)" class="w-20 text-right px-2 py-1 bg-slate-50 border rounded-lg text-xs font-semibold">
          </td>
          <td class="p-3 text-center">
            <div class="inline-flex items-center space-x-1">
              <input type="number" min="1" max="${item.maxStock}" value="${item.qty}" onchange="updateSellItemQty(${idx}, this.value)" class="w-14 text-center px-1 py-1 bg-white border border-slate-300 rounded-lg text-xs font-bold">
              <span class="text-[10px] text-slate-400">/ ${item.maxStock}</span>
            </div>
          </td>
          <td class="p-3 text-right font-bold text-slate-900">₹ ${item.subtotal.toFixed(2)}</td>
          <td class="p-3 text-center">
            <button type="button" onclick="removeSellItem(${idx})" class="text-red-500 hover:text-red-700 p-1"><i class="fa-solid fa-trash-can"></i></button>
          </td>
        `;
        tbody.appendChild(tr);
      });

      document.getElementById('bill-items-count').innerText = `${currentSellItems.length} items`;
      document.getElementById('bill-total-amount').innerText = `₹ ${grandTotal.toFixed(2)}`;
      document.getElementById('bill-amount-words').innerText = numberToWords(grandTotal);
    }

    // Sell Validation & Print Workflow
    function submitSellBill(printMode) {
      const patientName = document.getElementById('bill-patient-name').value.trim();
      const mobile = document.getElementById('bill-mobile').value.trim();
      const doctor = document.getElementById('bill-doctor').value;
      const surgery = document.getElementById('bill-surgery').value.trim();
      const billDate = document.getElementById('bill-date').value;
      const prefix = document.getElementById('bill-prefix').value;
      const age = document.getElementById('bill-age').value.trim();
      const sex = document.getElementById('bill-sex').value;
      const ip = document.getElementById('bill-ip').value.trim();
      const uhid = document.getElementById('bill-uhid').value.trim();

      if (!patientName) {
        showToast('Please enter Patient Name!', 'error');
        return;
      }

      // STRICT MOBILE VALIDATION: exactly 10 digits, not all zeros or dummy sequence
      const mobileRegex = /^[1-9][0-9]{9}$/;
      const isAllIdentical = /^(\d)\1{9}$/.test(mobile);
      if (!mobileRegex.test(mobile) || isAllIdentical) {
        showToast('Invalid Mobile Number! Must be exactly 10 valid digits and not dummy numbers like 0000000000 or repetitive digits.', 'error');
        document.getElementById('bill-mobile').focus();
        return;
      }

      if (currentSellItems.length === 0) {
        showToast('Cannot sell: Please add at least 1 medicine item!', 'error');
        return;
      }

      // Check stock availability again
      for (const item of currentSellItems) {
        const liveMed = medicines.find(m => m.id === item.medId);
        if (!liveMed || liveMed.qty < item.qty) {
          showToast(`Insufficient stock for ${item.name}! Available: ${liveMed ? liveMed.qty : 0}`, 'error');
          return;
        }
      }

      // Deduct from live stock
      currentSellItems.forEach(item => {
        const liveMed = medicines.find(m => m.id === item.medId);
        if (liveMed) {
          liveMed.qty -= item.qty;
        }
      });
      setStore('medicines', medicines);
      renderOTInventoryTable();

      const totalAmount = currentSellItems.reduce((acc, curr) => acc + curr.subtotal, 0);
      const billRecord = {
        billNo: 'OTB-' + Math.floor(100000 + Math.random() * 900000),
        date: billDate || new Date().toISOString().slice(0, 10),
        prefix,
        patientName,
        age: age ? age + ' Y' : 'N/A',
        sex,
        mobile,
        ip: ip || 'N/A',
        uhid: uhid || 'N/A',
        doctor,
        surgery: surgery || 'OT Procedure',
        items: JSON.parse(JSON.stringify(currentSellItems)),
        totalAmount,
        totalWords: numberToWords(totalAmount)
      };

      billsArchive.unshift(billRecord);
      setStore('bills', billsArchive);
      renderBillsArchive();

      closeSellModal();
      showToast('Sale recorded successfully & stock deducted!', 'success');

      // Open printable invoice modal with chosen letterhead option
      openInvoicePreview(billRecord, printMode);
    }

    function openInvoicePreview(bill, mode = 'with_letterhead') {
      const modal = document.getElementById('modal-invoice-preview');
      modal.classList.remove('hidden');
      modal.classList.add('flex');

      document.getElementById('invoice-modal-title').innerText = `Invoice - ${bill.billNo}`;
      document.getElementById('invoice-modal-mode-label').innerText = `Mode: ${mode === 'with_letterhead' ? 'With Letterhead' : 'Without Letterhead (Blank Space for Pre-printed Stationery)'}`;

      const headerWith = document.getElementById('invoice-header-with');
      const headerWithout = document.getElementById('invoice-header-without');
      const headerText = document.getElementById('inv-header-text');
      const headerImgContainer = document.getElementById('inv-header-img-container');
      const headerImg = document.getElementById('inv-header-img');

      if (mode === 'with_letterhead') {
        headerWith.classList.remove('hidden');
        headerWithout.classList.add('hidden');

        // Check configured mode
        if (appSettings.letterheadMode === 'image' && appSettings.letterheadLogo) {
          headerText.classList.add('hidden');
          headerImgContainer.classList.remove('hidden');
          headerImg.src = appSettings.letterheadLogo;
        } else {
          headerText.classList.remove('hidden');
          headerImgContainer.classList.add('hidden');
          document.getElementById('inv-hosp-name').innerText = appSettings.hospitalName;
          document.getElementById('inv-hosp-address').innerText = appSettings.hospitalAddress;
          document.getElementById('inv-hosp-phone').innerText = appSettings.hospitalPhone;
          document.getElementById('inv-hosp-email').innerText = appSettings.hospitalEmail;
        }
      } else {
        // Without letterhead: clean top space
        headerWith.classList.add('hidden');
        headerWithout.classList.remove('hidden');
      }

      // Populate Bill Info
      document.getElementById('inv-bill-no').innerText = bill.billNo;
      document.getElementById('inv-patient-name').innerText = `${bill.prefix} ${bill.patientName}`;
      document.getElementById('inv-age-sex').innerText = `${bill.age} / ${bill.sex}`;
      document.getElementById('inv-mobile').innerText = bill.mobile;
      document.getElementById('inv-date').innerText = bill.date;
      document.getElementById('inv-ip-uhid').innerText = `${bill.ip} / ${bill.uhid}`;
      document.getElementById('inv-doctor').innerText = bill.doctor;
      document.getElementById('inv-surgery').innerText = bill.surgery;

      const tbody = document.getElementById('inv-items-tbody');
      tbody.innerHTML = '';
      bill.items.forEach((item, idx) => {
        const tr = document.createElement('tr');
        tr.className = 'border-b border-slate-100';
        tr.innerHTML = `
          <td class="py-2 px-2 text-slate-500">${idx + 1}</td>
          <td class="py-2 px-2 font-bold text-slate-900">${item.name}</td>
          <td class="py-2 px-2 font-mono text-slate-600">${item.batch}</td>
          <td class="py-2 px-2 text-right">₹${Number(item.mrp).toFixed(2)}</td>
          <td class="py-2 px-2 text-center font-bold">${item.qty}</td>
          <td class="py-2 px-2 text-right font-bold text-slate-900">₹${Number(item.subtotal).toFixed(2)}</td>
        `;
        tbody.appendChild(tr);
      });

      document.getElementById('inv-total-words').innerText = bill.totalWords;
      document.getElementById('inv-total-num').innerText = `₹ ${Number(bill.totalAmount).toFixed(2)}`;
    }

    function closeInvoiceModal() {
      document.getElementById('modal-invoice-preview').classList.add('hidden');
      document.getElementById('modal-invoice-preview').classList.remove('flex');
    }

    // Toggle Archive view exclusively when 4. ARCHIVE is clicked
    function toggleArchiveView(forceState) {
      if (typeof forceState === 'boolean') {
        isArchiveVisible = forceState;
      } else {
        isArchiveVisible = !isArchiveVisible;
      }

      const archiveSection = document.getElementById('ot-archive-section');
      const toggleBtn = document.getElementById('btn-ot-archive-toggle');

      if (isArchiveVisible) {
        archiveSection.classList.remove('hidden');
        toggleBtn.className = 'flex-1 sm:flex-none px-4 py-2.5 rounded-2xl bg-brand-600 hover:bg-brand-500 text-white font-bold text-xs shadow-md shadow-brand-500/30 transition flex items-center justify-center space-x-1.5 active:scale-95 ring-2 ring-white/50';
        renderBillsArchive();
        archiveSection.scrollIntoView({ behavior: 'smooth' });
      } else {
        archiveSection.classList.add('hidden');
        toggleBtn.className = 'flex-1 sm:flex-none px-4 py-2.5 rounded-2xl bg-slate-700 hover:bg-slate-600 text-white font-bold text-xs shadow-md transition flex items-center justify-center space-x-1.5 active:scale-95';
      }
    }

    function renderBillsArchive() {
      const monthPicker = document.getElementById('archive-month-picker');
      if (!monthPicker.value) {
        const now = new Date();
        const yyyy = now.getFullYear();
        const mm = String(now.getMonth() + 1).padStart(2, '0');
        monthPicker.value = `${yyyy}-${mm}`;
      }

      const selectedMonth = monthPicker.value;
      const query = (document.getElementById('archive-bill-search').value || '').toLowerCase().trim();
      const tbody = document.getElementById('ot-bills-tbody');
      tbody.innerHTML = '';

      const filtered = billsArchive.filter(b => {
        const billMonth = b.date.slice(0, 7);
        const matchesMonth = billMonth === selectedMonth;
        const matchesQuery = b.patientName.toLowerCase().includes(query) ||
                             b.billNo.toLowerCase().includes(query) ||
                             b.uhid.toLowerCase().includes(query) ||
                             b.ip.toLowerCase().includes(query) ||
                             b.surgery.toLowerCase().includes(query);
        return matchesMonth && matchesQuery;
      });

      document.getElementById('stat-ot-month-bills').innerText = billsArchive.filter(b => b.date.slice(0, 7) === selectedMonth).length;

      if (filtered.length === 0) {
        tbody.innerHTML = `<tr><td colspan="8" class="text-center py-6 text-slate-400">No bills recorded for selected month (${selectedMonth}).</td></tr>`;
        return;
      }

      filtered.forEach(bill => {
        const tr = document.createElement('tr');
        tr.className = 'hover:bg-slate-50';
        tr.innerHTML = `
          <td class="py-3 px-4 font-mono font-bold text-brand-700">${bill.billNo}</td>
          <td class="py-3 px-4 text-slate-600">${bill.date}</td>
          <td class="py-3 px-4 font-semibold text-slate-900">${bill.prefix} ${bill.patientName} <span class="text-xs text-slate-400 block">${bill.mobile}</span></td>
          <td class="py-3 px-4 font-mono text-slate-600">${bill.uhid} / ${bill.ip}</td>
          <td class="py-3 px-4 text-slate-700">${bill.doctor} <span class="text-xs text-brand-600 block">${bill.surgery}</span></td>
          <td class="py-3 px-4 text-center font-bold">${bill.items.length}</td>
          <td class="py-3 px-4 text-right font-black text-slate-900">₹ ${Number(bill.totalAmount).toFixed(2)}</td>
          <td class="py-3 px-4 text-center space-x-1 whitespace-nowrap">
            <button onclick="reprintBill('${bill.billNo}', 'with_letterhead')" class="px-2 py-1 rounded bg-brand-50 hover:bg-brand-100 text-brand-700 text-xs font-bold transition" title="Print With Letterhead">
              <i class="fa-solid fa-stamp mr-1"></i>With L-Head
            </button>
            <button onclick="reprintBill('${bill.billNo}', 'without_letterhead')" class="px-2 py-1 rounded bg-slate-100 hover:bg-slate-200 text-slate-800 text-xs font-bold transition" title="Print Without Letterhead">
              <i class="fa-solid fa-file-invoice mr-1"></i>Plain
            </button>
          </td>
        `;
        tbody.appendChild(tr);
      });
    }

    function reprintBill(billNo, mode) {
      const bill = billsArchive.find(b => b.billNo === billNo);
      if (bill) openInvoicePreview(bill, mode);
    }

    function exportBillsArchiveToExcel() {
      let csvContent = 'data:text/csv;charset=utf-8,Bill No,Date,Patient Name,Mobile,Age,Sex,UHID,IP No,Doctor,Surgery,Items Count,Total Amount\n';
      billsArchive.forEach(b => {
        csvContent += `"${b.billNo}","${b.date}","${b.prefix} ${b.patientName}","${b.mobile}","${b.age}","${b.sex}","${b.uhid}","${b.ip}","${b.doctor}","${b.surgery}",${b.items.length},${b.totalAmount}\n`;
      });
      const encodedUri = encodeURI(csvContent);
      const link = document.createElement('a');
      link.setAttribute('href', encodedUri);
      link.setAttribute('download', `OT_Bills_Archive_${new Date().toISOString().slice(0, 10)}.csv`);
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }

    function openTemplatesModal() {
      resetTemplateEditor();
      renderTemplatesList();
      document.getElementById('modal-templates').classList.remove('hidden');
      document.getElementById('modal-templates').classList.add('flex');
    }

    function closeTemplatesModal() {
      document.getElementById('modal-templates').classList.add('hidden');
      document.getElementById('modal-templates').classList.remove('flex');
    }

    function resetTemplateEditor() {
      document.getElementById('tpl-edit-id').value = '';
      document.getElementById('tpl-name').value = '';
      document.getElementById('tpl-dept').value = '';
      document.getElementById('tpl-med-search').value = '';
      document.getElementById('tpl-pick-qty').value = '1';
      document.getElementById('tpl-search-results').classList.add('hidden');
      document.getElementById('tpl-search-results').innerHTML = '';
      document.getElementById('tpl-editor-title').innerText = 'Create Template';
      currentTemplateDraftItems = [];
      renderTemplateDraftTable();
    }

    // Search and Click engine for Template Creation (REPLACES DROPDOWN)
    function handleTemplateMedSearch(val) {
      const resultsBox = document.getElementById('tpl-search-results');
      const query = val.toLowerCase().trim();
      if (!query) {
        resultsBox.classList.add('hidden');
        resultsBox.innerHTML = '';
        return;
      }

      const matches = medicines.filter(m => m.name.toLowerCase().includes(query) || m.batch.toLowerCase().includes(query));
      if (matches.length === 0) {
        resultsBox.classList.remove('hidden');
        resultsBox.innerHTML = `<div class="p-3 text-xs text-slate-400 text-center">No medicines found matching "${val}".</div>`;
        return;
      }

      resultsBox.classList.remove('hidden');
      resultsBox.innerHTML = '';
      matches.forEach(item => {
        const itemDiv = document.createElement('div');
        itemDiv.className = 'p-2.5 hover:bg-purple-50 cursor-pointer flex items-center justify-between transition';
        itemDiv.innerHTML = `
          <div>
            <p class="font-bold text-slate-800 text-xs">${item.name}</p>
            <p class="text-[11px] text-slate-500">Batch: <span class="font-mono">${item.batch}</span> | MRP: ₹${Number(item.mrp).toFixed(2)} | Stock: ${item.qty}</p>
          </div>
          <button type="button" class="px-3 py-1 bg-purple-600 hover:bg-purple-700 text-white rounded-lg text-xs font-semibold">
            + Click to Add
          </button>
        `;
        itemDiv.onclick = () => {
          const qty = parseInt(document.getElementById('tpl-pick-qty').value, 10) || 1;
          addMedToTemplateDraftFromSearch(item, qty);
          resultsBox.classList.add('hidden');
          document.getElementById('tpl-med-search').value = '';
        };
        resultsBox.appendChild(itemDiv);
      });
    }

    function addMedToTemplateDraftFromSearch(med, qty) {
      const existing = currentTemplateDraftItems.find(i => i.name === med.name);
      if (existing) {
        existing.qty += qty;
      } else {
        currentTemplateDraftItems.push({
          medId: med.id,
          name: med.name,
          batch: med.batch,
          qty,
          mrp: Number(med.mrp)
        });
      }
      renderTemplateDraftTable();
      showToast(`Added ${med.name} (${qty}) to template package`, 'info');
    }

    function removeDraftTemplateItem(idx) {
      currentTemplateDraftItems.splice(idx, 1);
      renderTemplateDraftTable();
    }

    function renderTemplateDraftTable() {
      const tbody = document.getElementById('tpl-draft-tbody');
      tbody.innerHTML = '';
      if (currentTemplateDraftItems.length === 0) {
        tbody.innerHTML = `<tr><td colspan="4" class="p-4 text-center text-slate-400">No items added to this package yet. Search above and click to add.</td></tr>`;
        return;
      }

      currentTemplateDraftItems.forEach((item, idx) => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td class="p-2.5 font-bold text-slate-800">${item.name}</td>
          <td class="p-2.5 text-center font-bold text-purple-700">${item.qty}</td>
          <td class="p-2.5 text-right font-medium">₹${Number(item.mrp).toFixed(2)}</td>
          <td class="p-2.5 text-center">
            <button onclick="removeDraftTemplateItem(${idx})" class="text-red-500 hover:text-red-700"><i class="fa-solid fa-trash-can"></i></button>
          </td>
        `;
        tbody.appendChild(tr);
      });
    }

    function saveSurgeryTemplate() {
      const name = document.getElementById('tpl-name').value.trim();
      const department = document.getElementById('tpl-dept').value.trim() || 'General';
      const editId = document.getElementById('tpl-edit-id').value;

      if (!name) return showToast('Please enter Package Name', 'error');
      if (currentTemplateDraftItems.length === 0) return showToast('Add at least one medicine item to package', 'error');

      if (editId) {
        const idx = templates.findIndex(t => t.id === editId);
        if (idx !== -1) {
          templates[idx] = { id: editId, name, department, items: [...currentTemplateDraftItems] };
          showToast('Template package updated!', 'success');
        }
      } else {
        templates.push({
          id: 'tpl-' + Date.now(),
          name,
          department,
          items: [...currentTemplateDraftItems]
        });
        showToast('Surgery template saved!', 'success');
      }

      setStore('templates', templates);
      resetTemplateEditor();
      renderTemplatesList();
      renderOTInventoryTable();
    }

    function renderTemplatesList() {
      const container = document.getElementById('templates-list-container');
      container.innerHTML = '';
      if (templates.length === 0) {
        container.innerHTML = `<p class="text-xs text-slate-400">No packages created yet.</p>`;
        return;
      }

      templates.forEach(tpl => {
        const card = document.createElement('div');
        card.className = 'border border-slate-200 rounded-2xl p-4 bg-white hover:border-purple-300 transition flex flex-col sm:flex-row sm:items-center justify-between gap-3 shadow-xs';
        card.innerHTML = `
          <div>
            <div class="flex items-center space-x-2">
              <span class="px-2 py-0.5 bg-purple-100 text-purple-700 text-[10px] font-bold rounded-md uppercase">${tpl.department}</span>
              <h5 class="text-sm font-bold text-slate-900">${tpl.name}</h5>
            </div>
            <p class="text-xs text-slate-500 mt-1">${tpl.items.length} Medicines: ${tpl.items.map(i => i.name + ' (' + i.qty + ')').join(', ')}</p>
          </div>
          <div class="flex items-center space-x-2">
            <button onclick="editTemplate('${tpl.id}')" class="px-3 py-1.5 rounded-xl border border-slate-200 text-slate-700 hover:bg-slate-50 text-xs font-semibold">Edit</button>
            <button onclick="deleteTemplate('${tpl.id}')" class="px-3 py-1.5 rounded-xl border border-red-200 text-red-600 hover:bg-red-50 text-xs font-semibold">Delete</button>
            <button onclick="applyTemplateDirectlyToSell('${tpl.id}')" class="px-3.5 py-1.5 rounded-xl bg-purple-600 hover:bg-purple-700 text-white text-xs font-bold shadow-xs">Use in Sell</button>
          </div>
        `;
        container.appendChild(card);
      });
    }

    function editTemplate(id) {
      const t = templates.find(x => x.id === id);
      if (!t) return;
      document.getElementById('tpl-edit-id').value = t.id;
      document.getElementById('tpl-name').value = t.name;
      document.getElementById('tpl-dept').value = t.department;
      document.getElementById('tpl-editor-title').innerText = 'Edit Template';
      currentTemplateDraftItems = JSON.parse(JSON.stringify(t.items));
      renderTemplateDraftTable();
    }

    function deleteTemplate(id) {
      showConfirmModal('Delete Package', 'Delete this template package?', () => {
        templates = templates.filter(t => t.id !== id);
        setStore('templates', templates);
        renderTemplatesList();
        renderOTInventoryTable();
        showToast('Template deleted', 'info');
      });
    }

    function applyTemplateDirectlyToSell(tplId) {
      closeTemplatesModal();
      openSellModal();
      loadTemplateIntoSellState(tplId);
    }

    function promptApplyTemplateInSell() {
      if (templates.length === 0) {
        showToast('No templates created yet! Create one in 3. TEMPLATES.', 'warning');
        return;
      }
      const tplNames = templates.map((t, idx) => `${idx + 1}. ${t.name} (${t.items.length} items)`).join('\n');
      const choice = prompt(`Select template number to load:\n\n${tplNames}\n\nEnter number (1 - ${templates.length}):`);
      const num = parseInt(choice, 10);
      if (num > 0 && num <= templates.length) {
        loadTemplateIntoSellState(templates[num - 1].id);
      }
    }

    function loadTemplateIntoSellState(tplId) {
      const t = templates.find(x => x.id === tplId);
      if (!t) return;

      document.getElementById('bill-surgery').value = t.name;
      let addedCount = 0;

      t.items.forEach(item => {
        const liveMed = medicines.find(m => m.name.toLowerCase() === item.name.toLowerCase()) || medicines.find(m => m.id === item.medId);
        if (liveMed) {
          const existing = currentSellItems.find(x => x.medId === liveMed.id);
          const reqQty = item.qty;
          if (existing) {
            existing.qty += reqQty;
            existing.subtotal = existing.qty * existing.mrp;
          } else {
            currentSellItems.push({
              medId: liveMed.id,
              name: liveMed.name,
              batch: liveMed.batch,
              mrp: Number(liveMed.mrp),
              qty: reqQty,
              maxStock: liveMed.qty,
              subtotal: reqQty * Number(liveMed.mrp)
            });
          }
          addedCount++;
        }
      });

      renderBillItemsTable();
      showToast(`Loaded ${addedCount} items from '${t.name}'!`, 'success');
    }

    let currentPurchaseFilter = 'all';

    function filterPurchaseOrders(status) {
      currentPurchaseFilter = status;
      document.querySelectorAll('.purchase-filter-btn').forEach(btn => {
        if (btn.getAttribute('data-status') === status) {
          btn.className = 'purchase-filter-btn px-3 py-1.5 text-xs font-bold rounded-xl bg-teal-600 text-white';
        } else {
          btn.className = 'purchase-filter-btn px-3 py-1.5 text-xs font-bold rounded-xl text-slate-600 hover:bg-slate-100';
        }
      });
      renderPurchaseOrders();
    }

    function renderPurchaseOrders() {
      const container = document.getElementById('purchase-orders-list');
      const query = (document.getElementById('purchase-search').value || '').toLowerCase().trim();
      const monthFilter = document.getElementById('purchase-month-filter').value;
      container.innerHTML = '';

      const filtered = purchaseOrders.filter(po => {
        const matchesStatus = currentPurchaseFilter === 'all' || po.status === currentPurchaseFilter;
        const matchesQuery = po.stockist.toLowerCase().includes(query) || po.poNumber.toLowerCase().includes(query);
        const matchesMonth = !monthFilter || po.date.startsWith(monthFilter);
        return matchesStatus && matchesQuery && matchesMonth;
      });

      if (filtered.length === 0) {
        container.innerHTML = `
          <div class="bg-white rounded-3xl p-8 text-center border border-slate-200 text-slate-400">
            <i class="fa-solid fa-box-open text-3xl mb-2 text-slate-300"></i>
            <p class="text-sm">No orders found.</p>
          </div>
        `;
        return;
      }

      filtered.forEach(po => {
        const statusColors = {
          pending: 'bg-amber-100 text-amber-800 border-amber-200',
          completed: 'bg-emerald-100 text-emerald-800 border-emerald-200',
          cancelled: 'bg-slate-100 text-slate-700 border-slate-200',
          returned: 'bg-rose-100 text-rose-800 border-rose-200'
        };

        // Calculate days elapsed for overdue tags
        const orderDate = new Date(po.date);
        const today = new Date();
        const diffTime = today - orderDate;
        const elapsedDays = Math.floor(diffTime / (1000 * 60 * 60 * 24));
        const isDeliveryOverdue = po.status !== 'completed' && po.status !== 'cancelled' && elapsedDays > 30;
        const isPaymentOverdue = po.paymentStatus !== 'paid' && po.status !== 'cancelled' && elapsedDays > 30;

        const card = document.createElement('div');
        card.className = `bg-white rounded-3xl border ${isDeliveryOverdue || isPaymentOverdue ? 'border-red-300 ring-1 ring-red-200' : 'border-slate-200'} p-5 shadow-xs hover:border-teal-300 transition space-y-4`;
        
        let returnDetailsHtml = '';
        if (po.status === 'returned' && po.returnDetails) {
          const ret = po.returnDetails;
          returnDetailsHtml = `
            <div class="bg-rose-50 border border-rose-200 rounded-2xl p-3 text-xs text-rose-900 space-y-1">
              <div class="font-bold flex items-center space-x-1"><i class="fa-solid fa-arrow-rotate-left"></i><span>Return Details:</span></div>
              <p>Date: <strong>${ret.date}</strong> | Given By: <strong>${ret.givenBy}</strong> | Taken By: <strong>${ret.takenBy}</strong></p>
              <p>Settlement: <strong>${ret.type === 'refund' ? 'Refund / Credit Note' : 'Replacement Given'}</strong></p>
              ${ret.replacementNotes ? `<p class="italic">Replacement: ${ret.replacementNotes}</p>` : ''}
            </div>
          `;
        }

        let paymentHtml = '';
        if (po.paymentStatus === 'paid') {
          paymentHtml = `
            <span class="inline-flex items-center text-xs font-bold text-emerald-700 bg-emerald-50 px-2.5 py-1 rounded-xl border border-emerald-200">
              <i class="fa-solid fa-circle-check mr-1.5"></i> Paid on ${po.paidDate || 'N/A'} via ${po.paymentMode || 'UPI'}
            </span>
            ${po.upiScreenshot ? `
              <button onclick="previewUPIScreenshot('${po.poNumber}')" class="text-xs text-brand-600 font-bold underline hover:text-brand-800">
                View Receipt
              </button>
            ` : ''}
          `;
        } else {
          paymentHtml = `
            <button onclick="openRecordPaymentModal('${po.poNumber}')" class="px-3 py-1.5 rounded-xl border border-emerald-300 bg-emerald-50 hover:bg-emerald-100 text-emerald-800 text-xs font-bold transition">
              <i class="fa-solid fa-indian-rupee-sign mr-1"></i> Record Payment
            </button>
          `;
        }

        card.innerHTML = `
          <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-2 border-b border-slate-100 pb-3">
            <div class="flex flex-wrap items-center gap-2">
              <span class="px-3 py-1 rounded-xl text-xs font-bold uppercase border ${statusColors[po.status] || 'bg-slate-100'}">${po.status}</span>
              <h4 class="font-bold text-slate-900 text-base font-mono">${po.poNumber}</h4>
              <span class="text-xs text-slate-500">${po.date} (${elapsedDays} days ago)</span>
              ${isDeliveryOverdue ? `<span class="px-2 py-0.5 bg-red-100 text-red-700 border border-red-300 text-[10px] font-bold rounded-lg uppercase tracking-wider animate-pulse"><i class="fa-solid fa-clock-rotate-left mr-1"></i>Delivery Overdue (${elapsedDays - 30}d)</span>` : ''}
              ${isPaymentOverdue ? `<span class="px-2 py-0.5 bg-amber-100 text-amber-800 border border-amber-300 text-[10px] font-bold rounded-lg uppercase tracking-wider animate-pulse"><i class="fa-solid fa-triangle-exclamation mr-1"></i>Payment Overdue (${elapsedDays - 30}d)</span>` : ''}
            </div>
            <div class="text-xs font-bold text-slate-700">
              Items: ${po.items.length} medicines
            </div>
          </div>

          <div class="grid grid-cols-1 sm:grid-cols-2 gap-2 text-xs">
            <p><span class="text-slate-500">Stockist:</span> <strong class="text-slate-800">${po.stockist}</strong></p>
          </div>

          <!-- Items pills (Only Name and Quantity) -->
          <div class="bg-slate-50 rounded-2xl p-3 border border-slate-100 text-xs text-slate-700">
            <span class="font-bold text-slate-500 block text-[11px] mb-1">Medicines in this Order:</span>
            <div class="flex flex-wrap gap-2">
              ${po.items.map(it => `<span class="bg-white border px-2.5 py-1 rounded-lg font-medium text-slate-800">${it.name} - Qty: <strong>${it.qty}</strong></span>`).join('')}
            </div>
          </div>

          ${returnDetailsHtml}

          <!-- Action Footers -->
          <div class="flex flex-wrap items-center justify-between gap-3 pt-2">
            <div class="flex items-center space-x-2">
              ${paymentHtml}
            </div>

            <div class="flex items-center space-x-2">
              ${po.status === 'pending' ? `
                <button onclick="markOrderDelivered('${po.poNumber}')" class="px-3.5 py-1.5 rounded-xl bg-teal-600 hover:bg-teal-700 text-white text-xs font-bold transition">
                  <i class="fa-solid fa-truck-ramp-box mr-1"></i> Delivered
                </button>
                <button onclick="markOrderCancelled('${po.poNumber}')" class="px-3.5 py-1.5 rounded-xl border border-red-200 text-red-600 hover:bg-red-50 text-xs font-semibold transition">
                  Cancel
                </button>
              ` : ''}

              ${po.status === 'completed' ? `
                <button onclick="promptReturnOrder('${po.poNumber}')" class="px-3.5 py-1.5 rounded-xl border border-rose-300 bg-rose-50 hover:bg-rose-100 text-rose-800 text-xs font-bold transition">
                  <i class="fa-solid fa-arrow-rotate-left mr-1"></i> Return
                </button>
              ` : ''}
            </div>
          </div>
        `;

        container.appendChild(card);
      });
      updateNotificationBadges();
    }

    // ==========================================
    // NOTIFICATION CENTER: 1-MONTH OVERDUE ENGINE
    // ==========================================
    function calculateOverduePurchaseAlerts() {
      const today = new Date();
      const overdueDeliveries = [];
      const overduePayments = [];

      purchaseOrders.forEach(po => {
        const orderDate = new Date(po.date);
        const diffTime = today - orderDate;
        const elapsedDays = Math.floor(diffTime / (1000 * 60 * 60 * 24));

        // Delivery overdue: Pending / Not delivered and > 30 days
        if (po.status !== 'completed' && po.status !== 'cancelled' && elapsedDays > 30) {
          overdueDeliveries.push({
            po,
            elapsedDays,
            daysOverdue: elapsedDays - 30,
            type: 'delivery'
          });
        }

        // Payment overdue: Payment not done and > 30 days (excluding cancelled orders)
        if (po.paymentStatus !== 'paid' && po.status !== 'cancelled' && elapsedDays > 30) {
          overduePayments.push({
            po,
            elapsedDays,
            daysOverdue: elapsedDays - 30,
            type: 'payment'
          });
        }
      });

      return { overdueDeliveries, overduePayments, total: overdueDeliveries.length + overduePayments.length };
    }

    function updateNotificationBadges() {
      const { overdueDeliveries, overduePayments, total } = calculateOverduePurchaseAlerts();
      const badge = document.getElementById('notif-badge');

      if (total > 0) {
        badge.innerText = total;
        badge.classList.remove('hidden');
      } else {
        badge.classList.add('hidden');
      }

      document.getElementById('notif-count-all').innerText = total;
      document.getElementById('notif-count-payments').innerText = overduePayments.length;
      document.getElementById('notif-count-deliveries').innerText = overdueDeliveries.length;
    }

    function openNotificationCenter() {
      document.getElementById('modal-notification-center').classList.remove('hidden');
      document.getElementById('modal-notification-center').classList.add('flex');
      renderNotificationCenterItems();
    }

    function closeNotificationCenter() {
      document.getElementById('modal-notification-center').classList.add('hidden');
      document.getElementById('modal-notification-center').classList.remove('flex');
    }

    function filterNotificationView(filter) {
      currentNotifFilter = filter;
      const tabs = ['all', 'payments', 'deliveries'];
      tabs.forEach(t => {
        const btn = document.getElementById(`notif-tab-${t}`);
        if (t === filter) {
          btn.className = 'py-3 border-b-2 border-brand-600 text-brand-600 whitespace-nowrap font-bold';
        } else {
          btn.className = 'py-3 border-b-2 border-transparent text-slate-600 hover:text-slate-900 whitespace-nowrap font-bold';
        }
      });
      renderNotificationCenterItems();
    }

    function renderNotificationCenterItems() {
      const { overdueDeliveries, overduePayments, total } = calculateOverduePurchaseAlerts();
      const container = document.getElementById('notif-items-list');
      const emptyState = document.getElementById('notif-empty-state');
      container.innerHTML = '';

      let itemsToRender = [];
      if (currentNotifFilter === 'all') {
        itemsToRender = [...overdueDeliveries, ...overduePayments];
      } else if (currentNotifFilter === 'payments') {
        itemsToRender = overduePayments;
      } else if (currentNotifFilter === 'deliveries') {
        itemsToRender = overdueDeliveries;
      }

      if (itemsToRender.length === 0) {
        emptyState.classList.remove('hidden');
        return;
      }

      emptyState.classList.add('hidden');

      itemsToRender.forEach(alertItem => {
        const { po, elapsedDays, daysOverdue, type } = alertItem;
        const card = document.createElement('div');
        const isDelivery = type === 'delivery';

        card.className = `p-4 rounded-2xl border ${isDelivery ? 'bg-red-50/50 border-red-200' : 'bg-amber-50/50 border-amber-200'} transition shadow-xs space-y-2.5`;
        card.innerHTML = `
          <div class="flex items-start justify-between gap-2">
            <div class="flex items-center space-x-2">
              <span class="w-8 h-8 rounded-xl ${isDelivery ? 'bg-red-100 text-red-600' : 'bg-amber-100 text-amber-700'} flex items-center justify-center font-bold text-xs">
                <i class="fa-solid ${isDelivery ? 'fa-truck-fast' : 'fa-hand-holding-dollar'}"></i>
              </span>
              <div>
                <div class="flex items-center space-x-2">
                  <span class="text-xs font-black font-mono text-slate-900">${po.poNumber}</span>
                  <span class="px-2 py-0.5 rounded text-[10px] font-extrabold uppercase ${isDelivery ? 'bg-red-100 text-red-800' : 'bg-amber-100 text-amber-900'}">
                    ${isDelivery ? 'Overdue Delivery' : 'Overdue Payment'}
                  </span>
                </div>
                <p class="text-xs font-semibold text-slate-700">${po.stockist}</p>
              </div>
            </div>
            <div class="text-right">
              <span class="text-[11px] font-bold text-red-600 block">${daysOverdue} days past 1-month grace</span>
              <span class="text-[10px] text-slate-400">Ordered: ${po.date} (${elapsedDays}d ago)</span>
            </div>
          </div>

          <div class="text-xs text-slate-600 bg-white/80 p-2.5 rounded-xl border border-slate-200/60">
            <span class="text-[10px] font-bold uppercase tracking-wider text-slate-400 block mb-0.5">Order Medicines:</span>
            <span>${po.items.map(i => `${i.name} (Qty: ${i.qty})`).join(', ')}</span>
          </div>

          <div class="flex items-center justify-between pt-1">
            <span class="text-[11px] font-semibold text-slate-500">Status: <strong class="uppercase text-slate-800">${po.status}</strong> | Payment: <strong class="uppercase text-slate-800">${po.paymentStatus}</strong></span>
            
            <div class="flex items-center space-x-2">
              ${isDelivery ? `
                <button onclick="handleNotifDeliveryAction('${po.poNumber}')" class="px-3 py-1.5 bg-teal-600 hover:bg-teal-700 text-white rounded-xl text-xs font-bold transition flex items-center space-x-1">
                  <i class="fa-solid fa-truck-ramp-box text-[11px]"></i>
                  <span>Mark Delivered</span>
                </button>
              ` : `
                <button onclick="handleNotifPaymentAction('${po.poNumber}')" class="px-3 py-1.5 bg-emerald-600 hover:bg-emerald-700 text-white rounded-xl text-xs font-bold transition flex items-center space-x-1">
                  <i class="fa-solid fa-indian-rupee-sign text-[11px]"></i>
                  <span>Record Payment</span>
                </button>
              `}
              <button onclick="navigateToOrderInPurchase('${po.poNumber}')" class="px-2.5 py-1.5 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl text-xs font-semibold transition" title="View in Purchase Orders">
                <i class="fa-solid fa-arrow-up-right-from-square"></i>
              </button>
            </div>
          </div>
        `;
        container.appendChild(card);
      });
    }

    function handleNotifDeliveryAction(poNumber) {
      markOrderDelivered(poNumber);
      renderNotificationCenterItems();
      updateNotificationBadges();
    }

    function handleNotifPaymentAction(poNumber) {
      closeNotificationCenter();
      openRecordPaymentModal(poNumber);
    }

    function navigateToOrderInPurchase(poNumber) {
      closeNotificationCenter();
      switchMainTab('purchase');
      document.getElementById('purchase-search').value = poNumber;
      filterPurchaseOrders('all');
    }

    function openCreateOrderModal() {
      populateStockistDropdowns();
      currentOrderDraftItems = [];
      document.getElementById('po-date').value = new Date().toISOString().slice(0, 10);
      document.getElementById('po-item-name').value = '';
      document.getElementById('po-item-qty').value = '';
      renderOrderDraftTable();
      document.getElementById('modal-create-order').classList.remove('hidden');
      document.getElementById('modal-create-order').classList.add('flex');
    }

    function closeCreateOrderModal() {
      document.getElementById('modal-create-order').classList.add('hidden');
      document.getElementById('modal-create-order').classList.remove('flex');
    }

    function addItemToOrderDraft() {
      const name = document.getElementById('po-item-name').value.trim();
      const qty = parseInt(document.getElementById('po-item-qty').value, 10);

      if (!name || isNaN(qty) || qty <= 0) {
        showToast('Please enter medicine name and valid quantity!', 'error');
        return;
      }

      currentOrderDraftItems.push({
        name,
        qty
      });

      document.getElementById('po-item-name').value = '';
      document.getElementById('po-item-qty').value = '';
      document.getElementById('po-item-name').focus();
      renderOrderDraftTable();
    }

    function removeOrderDraftItem(idx) {
      currentOrderDraftItems.splice(idx, 1);
      renderOrderDraftTable();
    }

    function renderOrderDraftTable() {
      const tbody = document.getElementById('po-draft-tbody');
      tbody.innerHTML = '';
      if (currentOrderDraftItems.length === 0) {
        tbody.innerHTML = `<tr><td colspan="4" class="p-4 text-center text-slate-400">No items added to this order yet.</td></tr>`;
        document.getElementById('po-draft-total').innerText = 'Total Items: 0';
        return;
      }

      currentOrderDraftItems.forEach((it, idx) => {
        const tr = document.createElement('tr');
        tr.innerHTML = `
          <td class="p-2.5 text-slate-400">${idx + 1}</td>
          <td class="p-2.5 font-bold text-slate-800">${it.name}</td>
          <td class="p-2.5 text-center font-bold text-teal-700">${it.qty}</td>
          <td class="p-2.5 text-center">
            <button onclick="removeOrderDraftItem(${idx})" class="text-red-500 hover:text-red-700"><i class="fa-solid fa-trash-can"></i></button>
          </td>
        `;
        tbody.appendChild(tr);
      });

      document.getElementById('po-draft-total').innerText = `Total Items: ${currentOrderDraftItems.length}`;
    }

    function submitPurchaseOrder() {
      const stockist = document.getElementById('po-stockist').value;
      const date = document.getElementById('po-date').value;
      if (!stockist) return showToast('Please select a stockist', 'error');
      if (currentOrderDraftItems.length === 0) return showToast('Add at least 1 medicine to order', 'error');

      const newPO = {
        poNumber: 'PO-' + Math.floor(100000 + Math.random() * 900000),
        date: date || new Date().toISOString().slice(0, 10),
        stockist,
        items: [...currentOrderDraftItems],
        status: 'pending', // pending, completed, cancelled, returned
        paymentStatus: 'unpaid', // unpaid, paid
        paidDate: null,
        paymentMode: null,
        upiScreenshot: null,
        returnDetails: null
      };

      purchaseOrders.unshift(newPO);
      setStore('purchase_orders', purchaseOrders);
      closeCreateOrderModal();
      renderPurchaseOrders();
      showToast('Purchase Order generated successfully!', 'success');
    }

    function markOrderDelivered(poNumber) {
      showConfirmModal('Order Delivered', 'Mark this order as DELIVERED? It will move to Completed.', () => {
        const po = purchaseOrders.find(p => p.poNumber === poNumber);
        if (po) {
          po.status = 'completed';
          setStore('purchase_orders', purchaseOrders);
          renderPurchaseOrders();
          showToast(`Order ${poNumber} marked as Completed!`, 'success');
        }
      });
    }

    function markOrderCancelled(poNumber) {
      showConfirmModal('Cancel Order', 'Are you sure you want to cancel this order?', () => {
        const po = purchaseOrders.find(p => p.poNumber === poNumber);
        if (po) {
          po.status = 'cancelled';
          setStore('purchase_orders', purchaseOrders);
          renderPurchaseOrders();
          showToast(`Order ${poNumber} cancelled.`, 'info');
        }
      });
    }

    // Payment Dialog with OPTIONAL screenshot
    function openRecordPaymentModal(poNumber) {
      const po = purchaseOrders.find(p => p.poNumber === poNumber);
      if (!po) return;

      paymentScreenshotDraft = null;
      document.getElementById('pay-po-number').value = poNumber;
      document.getElementById('pay-date').value = new Date().toISOString().slice(0, 10);
      document.getElementById('pay-screenshot-file').value = '';
      document.getElementById('pay-preview-box').classList.add('hidden');
      document.getElementById('modal-record-payment').classList.remove('hidden');
      document.getElementById('modal-record-payment').classList.add('flex');
    }

    function closeRecordPaymentModal() {
      document.getElementById('modal-record-payment').classList.add('hidden');
      document.getElementById('modal-record-payment').classList.remove('flex');
    }

    document.getElementById('pay-screenshot-file').addEventListener('change', function(e) {
      const file = e.target.files[0];
      if (file) {
        const reader = new FileReader();
        reader.onload = function(evt) {
          paymentScreenshotDraft = evt.target.result;
          document.getElementById('pay-preview-img').src = evt.target.result;
          document.getElementById('pay-preview-box').classList.remove('hidden');
        };
        reader.readAsDataURL(file);
      } else {
        paymentScreenshotDraft = null;
        document.getElementById('pay-preview-box').classList.add('hidden');
      }
    });

    function saveOrderPayment() {
      const poNumber = document.getElementById('pay-po-number').value;
      const mode = document.getElementById('pay-mode').value;
      const date = document.getElementById('pay-date').value;

      const po = purchaseOrders.find(p => p.poNumber === poNumber);
      if (!po) return;

      po.paymentStatus = 'paid';
      po.paymentMode = mode;
      po.paidDate = date || new Date().toISOString().slice(0, 10);
      // Screenshot is optional - attach if provided
      if (paymentScreenshotDraft) {
        po.upiScreenshot = paymentScreenshotDraft;
      }

      setStore('purchase_orders', purchaseOrders);
      closeRecordPaymentModal();
      renderPurchaseOrders();
      updateNotificationBadges();
      showToast('Payment recorded successfully!', 'success');
    }

    function previewUPIScreenshot(poNumber) {
      const po = purchaseOrders.find(p => p.poNumber === poNumber);
      if (po && po.upiScreenshot) {
        document.getElementById('upi-preview-img').src = po.upiScreenshot;
        document.getElementById('modal-upi-preview').classList.remove('hidden');
        document.getElementById('modal-upi-preview').classList.add('flex');
      } else {
        showToast('No receipt image attached to this payment.', 'info');
      }
    }

    function promptReturnOrder(poNumber) {
      document.getElementById('ret-order-id').value = poNumber;
      document.getElementById('ret-date').value = new Date().toISOString().slice(0, 10);
      document.getElementById('ret-given-by').value = '';
      document.getElementById('ret-taken-by').value = '';
      document.getElementById('ret-settlement-type').value = 'refund';
      document.getElementById('ret-replacement-notes').value = '';
      document.getElementById('ret-replacement-details-box').classList.add('hidden');
      document.getElementById('modal-return-order').classList.remove('hidden');
      document.getElementById('modal-return-order').classList.add('flex');
    }

    function closeReturnModal() {
      document.getElementById('modal-return-order').classList.add('hidden');
      document.getElementById('modal-return-order').classList.remove('flex');
    }

    function toggleReplacementFields(type) {
      const box = document.getElementById('ret-replacement-details-box');
      if (type === 'replacement') {
        box.classList.remove('hidden');
      } else {
        box.classList.add('hidden');
      }
    }

    function confirmOrderReturn() {
      const poNumber = document.getElementById('ret-order-id').value;
      const date = document.getElementById('ret-date').value;
      const givenBy = document.getElementById('ret-given-by').value.trim();
      const takenBy = document.getElementById('ret-taken-by').value.trim();
      const type = document.getElementById('ret-settlement-type').value;
      const replacementNotes = document.getElementById('ret-replacement-notes').value.trim();

      if (!givenBy || !takenBy) {
        showToast('Please enter Given By and Taken By names!', 'error');
        return;
      }

      const po = purchaseOrders.find(p => p.poNumber === poNumber);
      if (po) {
        po.status = 'returned';
        po.returnDetails = {
          date: date || new Date().toISOString().slice(0, 10),
          givenBy,
          takenBy,
          type,
          replacementNotes
        };
        setStore('purchase_orders', purchaseOrders);
        closeReturnModal();
        renderPurchaseOrders();
        showToast(`Order ${poNumber} marked as Returned!`, 'success');
      }
    }

    function exportPurchaseOrdersToExcel() {
      let csvContent = 'data:text/csv;charset=utf-8,PO Number,Date,Stockist,Status,Payment Status,Paid Date,Payment Mode,Items Count\n';
      purchaseOrders.forEach(p => {
        csvContent += `"${p.poNumber}","${p.date}","${p.stockist}","${p.status}","${p.paymentStatus}","${p.paidDate || ''}","${p.paymentMode || ''}",${p.items.length}\n`;
      });
      const encodedUri = encodeURI(csvContent);
      const link = document.createElement('a');
      link.setAttribute('href', encodedUri);
      link.setAttribute('download', `Purchase_Orders_${new Date().toISOString().slice(0, 10)}.csv`);
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }

    function renderVendorsAndOffers() {
      const query = (document.getElementById('vendor-smart-search').value || '').toLowerCase().trim();
      const repContainer = document.getElementById('vendor-list-container');
      const offerContainer = document.getElementById('offers-list-container');

      repContainer.innerHTML = '';
      offerContainer.innerHTML = '';

      // Dual search filter
      const filteredVendors = vendors.filter(v => {
        return v.name.toLowerCase().includes(query) ||
               v.company.toLowerCase().includes(query) ||
               v.products.toLowerCase().includes(query) ||
               v.mobile.includes(query);
      });

      const filteredOffers = repOffers.filter(o => {
        return o.product.toLowerCase().includes(query) ||
               o.rep.toLowerCase().includes(query) ||
               o.scheme.toLowerCase().includes(query);
      });

      document.getElementById('vendor-count-badge').innerText = `${filteredVendors.length} Reps`;
      document.getElementById('offer-count-badge').innerText = `${filteredOffers.length} Offers`;

      // Render Rep Cards
      if (filteredVendors.length === 0) {
        repContainer.innerHTML = `<p class="text-xs text-slate-400 p-4 text-center">No representative found.</p>`;
      } else {
        filteredVendors.forEach(ven => {
          const card = document.createElement('div');
          card.className = 'border border-slate-200 rounded-2xl p-4 bg-slate-50/50 hover:bg-white hover:border-indigo-300 transition shadow-xs space-y-2';
          card.innerHTML = `
            <div class="flex items-center justify-between">
              <div>
                <h4 class="font-bold text-slate-900 text-sm">${ven.name}</h4>
                <p class="text-xs font-semibold text-indigo-700">${ven.company}</p>
              </div>
              <div class="flex items-center space-x-1">
                <button onclick="editVendor('${ven.id}')" class="p-1 text-slate-400 hover:text-indigo-600"><i class="fa-solid fa-pen-to-square"></i></button>
                <button onclick="deleteVendor('${ven.id}')" class="p-1 text-slate-400 hover:text-red-600"><i class="fa-solid fa-trash-can"></i></button>
              </div>
            </div>

            <div class="text-xs text-slate-600 flex items-center space-x-2">
              <i class="fa-solid fa-phone text-emerald-600"></i>
              <span class="font-mono font-bold">${ven.mobile}</span>
            </div>

            <div class="pt-1 border-t border-slate-200/60">
              <span class="text-[10px] font-bold uppercase tracking-wider text-slate-400">Products:</span>
              <p class="text-xs text-slate-700 font-medium mt-0.5">${ven.products}</p>
            </div>
          `;
          repContainer.appendChild(card);
        });
      }

      // Render Offers Cards
      if (filteredOffers.length === 0) {
        offerContainer.innerHTML = `<p class="text-xs text-slate-400 p-4 text-center">No offers found.</p>`;
      } else {
        filteredOffers.forEach(off => {
          const card = document.createElement('div');
          card.className = 'border border-purple-200 rounded-2xl p-4 bg-purple-50/30 hover:bg-white hover:border-purple-400 transition shadow-xs space-y-2';
          card.innerHTML = `
            <div class="flex items-center justify-between">
              <div>
                <h4 class="font-bold text-slate-900 text-sm">${off.product}</h4>
                <p class="text-[11px] text-purple-700 font-medium">${off.rep}</p>
              </div>
              <div class="flex items-center space-x-1">
                <button onclick="editOffer('${off.id}')" class="p-1 text-slate-400 hover:text-purple-600"><i class="fa-solid fa-pen-to-square"></i></button>
                <button onclick="deleteOffer('${off.id}')" class="p-1 text-slate-400 hover:text-red-600"><i class="fa-solid fa-trash-can"></i></button>
              </div>
            </div>

            <div class="grid grid-cols-2 gap-2 text-xs py-1">
              <div class="bg-white p-2 rounded-xl border border-purple-100">
                <span class="text-slate-500 block text-[10px]">MRP:</span>
                <span class="font-bold text-slate-900 font-mono">₹ ${Number(off.mrp).toFixed(2)}</span>
              </div>
              <div class="bg-emerald-50 p-2 rounded-xl border border-emerald-200">
                <span class="text-emerald-700 block text-[10px] font-bold">C-Price:</span>
                <span class="font-black text-emerald-800 font-mono">₹ ${Number(off.cprice).toFixed(2)}</span>
              </div>
            </div>

            <div class="bg-purple-100/70 p-2 rounded-xl text-xs text-purple-900 font-bold flex items-center space-x-1.5">
              <i class="fa-solid fa-gift text-purple-700"></i>
              <span>Scheme: ${off.scheme}</span>
            </div>
          `;
          offerContainer.appendChild(card);
        });
      }
    }

    function openAddVendorModal() {
      document.getElementById('form-vendor').reset();
      document.getElementById('vendor-edit-id').value = '';
      document.getElementById('vendor-modal-title').innerText = 'Add Rep';
      document.getElementById('modal-add-vendor').classList.remove('hidden');
      document.getElementById('modal-add-vendor').classList.add('flex');
    }

    function closeAddVendorModal() {
      document.getElementById('modal-add-vendor').classList.add('hidden');
      document.getElementById('modal-add-vendor').classList.remove('flex');
    }

    function handleSaveVendor(e) {
      e.preventDefault();
      const editId = document.getElementById('vendor-edit-id').value;
      const name = document.getElementById('v-name').value.trim();
      const mobile = document.getElementById('v-mobile').value.trim();
      const company = document.getElementById('v-company').value.trim();
      const products = document.getElementById('v-products').value.trim();

      const mobileRegex = /^[0-9]{10}$/;
      if (!mobileRegex.test(mobile)) {
        showToast('Please enter a valid 10-digit mobile number!', 'error');
        return;
      }

      if (editId) {
        const idx = vendors.findIndex(v => v.id === editId);
        if (idx !== -1) {
          vendors[idx] = { id: editId, name, mobile, company, products };
          showToast('Rep updated!', 'success');
        }
      } else {
        vendors.push({
          id: 'ven-' + Date.now(),
          name,
          mobile,
          company,
          products
        });
        showToast('Rep added!', 'success');
      }

      setStore('vendors', vendors);
      closeAddVendorModal();
      renderVendorsAndOffers();
    }

    function editVendor(id) {
      const v = vendors.find(x => x.id === id);
      if (!v) return;
      openAddVendorModal();
      document.getElementById('vendor-modal-title').innerText = 'Edit Rep';
      document.getElementById('vendor-edit-id').value = v.id;
      document.getElementById('v-name').value = v.name;
      document.getElementById('v-mobile').value = v.mobile;
      document.getElementById('v-company').value = v.company;
      document.getElementById('v-products').value = v.products;
    }

    function deleteVendor(id) {
      showConfirmModal('Delete Rep', 'Remove this representative from directory?', () => {
        vendors = vendors.filter(v => v.id !== id);
        setStore('vendors', vendors);
        renderVendorsAndOffers();
        showToast('Rep removed', 'info');
      });
    }

    function openAddOfferModal() {
      document.getElementById('form-offer').reset();
      document.getElementById('offer-edit-id').value = '';
      document.getElementById('offer-modal-title').innerText = 'Add Offer Deal';
      document.getElementById('modal-add-offer').classList.remove('hidden');
      document.getElementById('modal-add-offer').classList.add('flex');
    }

    function closeAddOfferModal() {
      document.getElementById('modal-add-offer').classList.add('hidden');
      document.getElementById('modal-add-offer').classList.remove('flex');
    }

    function handleSaveOffer(e) {
      e.preventDefault();
      const editId = document.getElementById('offer-edit-id').value;
      const product = document.getElementById('off-product').value.trim();
      const rep = document.getElementById('off-rep').value.trim();
      const mrp = parseFloat(document.getElementById('off-mrp').value);
      const cprice = parseFloat(document.getElementById('off-cprice').value);
      const scheme = document.getElementById('off-scheme').value.trim();

      if (editId) {
        const idx = repOffers.findIndex(o => o.id === editId);
        if (idx !== -1) {
          repOffers[idx] = { id: editId, product, rep, mrp, cprice, scheme };
          showToast('Offer deal updated!', 'success');
        }
      } else {
        repOffers.push({
          id: 'off-' + Date.now(),
          product,
          rep,
          mrp,
          cprice,
          scheme
        });
        showToast('Offer deal added!', 'success');
      }

      setStore('offers', repOffers);
      closeAddOfferModal();
      renderVendorsAndOffers();
    }

    function editOffer(id) {
      const o = repOffers.find(x => x.id === id);
      if (!o) return;
      openAddOfferModal();
      document.getElementById('offer-modal-title').innerText = 'Edit Offer Deal';
      document.getElementById('offer-edit-id').value = o.id;
      document.getElementById('off-product').value = o.product;
      document.getElementById('off-rep').value = o.rep;
      document.getElementById('off-mrp').value = o.mrp;
      document.getElementById('off-cprice').value = o.cprice;
      document.getElementById('off-scheme').value = o.scheme;
    }

    function deleteOffer(id) {
      showConfirmModal('Delete Offer', 'Remove this deal from active offers?', () => {
        repOffers = repOffers.filter(o => o.id !== id);
        setStore('offers', repOffers);
        renderVendorsAndOffers();
        showToast('Offer removed', 'info');
      });
    }

    function refreshAllViews() {
      updateAppBrandingDisplay();
      populateDoctorDropdowns();
      populateStockistDropdowns();
      renderOTInventoryTable();
      renderPurchaseOrders();
      renderVendorsAndOffers();
      updateNotificationBadges();
    }

    window.addEventListener('DOMContentLoaded', () => {
      // Seed sample bills if empty
      if (billsArchive.length === 0) {
        billsArchive.push({
          billNo: 'OTB-940210',
          date: new Date().toISOString().slice(0, 10),
          prefix: 'Mr.',
          patientName: 'Kishore Gowda',
          age: '42 Y',
          sex: 'Male',
          mobile: '9845112233',
          ip: 'IP-2091',
          uhid: 'UH-88912',
          doctor: 'Dr. Rajesh Sharma, MS (Gen Surgery)',
          surgery: 'Laparoscopic Cholecystectomy Kit',
          items: [
            { name: 'Propofol 10mg/ml 20ml Inj', batch: 'PF-9081', mrp: 280, qty: 2, subtotal: 560 },
            { name: 'Atracurium Besylate 25mg/2.5ml', batch: 'AT-3342', mrp: 145, qty: 2, subtotal: 290 },
            { name: 'Vicryl 2-0 Suture NW2664', batch: 'VC-0911', mrp: 420, qty: 2, subtotal: 840 }
          ],
          totalAmount: 1690.00,
          totalWords: 'Rupees One Thousand Six Hundred and Ninety Only'
        });
        setStore('bills', billsArchive);
      }

      // Seed sample PO if empty (with one recent and one 45-day overdue item for demonstration)
      if (purchaseOrders.length === 0) {
        const dOverdue = new Date();
        dOverdue.setDate(dOverdue.getDate() - 45); // 45 days ago -> > 30 days overdue

        purchaseOrders.push({
          poNumber: 'PO-518290',
          date: new Date().toISOString().slice(0, 10),
          stockist: 'Apollo Medical Supplies Ltd',
          status: 'pending',
          paymentStatus: 'unpaid',
          paidDate: null,
          paymentMode: null,
          items: [
            { name: 'Propofol 10mg/ml 20ml Inj', qty: 50 },
            { name: 'Cefuroxime 1.5g Injection', qty: 30 }
          ]
        });

        purchaseOrders.push({
          poNumber: 'PO-390114',
          date: dOverdue.toISOString().slice(0, 10),
          stockist: 'MediSource Healthcare Logistics',
          status: 'pending',
          paymentStatus: 'unpaid',
          paidDate: null,
          paymentMode: null,
          items: [
            { name: 'Sevoflurane Inhalation 250ml', qty: 15 },
            { name: 'Atracurium Besylate 25mg/2.5ml', qty: 60 }
          ]
        });
        setStore('purchase_orders', purchaseOrders);
      }

      updateAppBrandingDisplay();
      populateDoctorDropdowns();
      populateStockistDropdowns();
      renderOTInventoryTable();
      updateNotificationBadges();
    });
  </script>
</body>
</html>
