<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AquaManager - Gestión APR</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        :root {
            --primary: #0066cc;
            --secondary: #00a8e8;
            --success: #28a745;
            --warning: #ffc107;
            --danger: #dc3545;
            --dark: #1a1a2e;
            --light: #f8f9fa;
            --sidebar-width: 280px;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            overflow-x: hidden;
        }

        /* Login Screen */
        .login-screen {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 9999;
            transition: opacity 0.5s, visibility 0.5s;
        }

        .login-screen.hidden {
            opacity: 0;
            visibility: hidden;
        }

        .login-container {
            background: rgba(255, 255, 255, 0.95);
            padding: 3rem;
            border-radius: 20px;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
            width: 90%;
            max-width: 400px;
            text-align: center;
            animation: slideUp 0.6s ease;
        }

        @keyframes slideUp {
            from { transform: translateY(50px); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }

        .login-logo {
            font-size: 4rem;
            color: var(--primary);
            margin-bottom: 1rem;
        }

        .login-container h1 {
            color: var(--dark);
            margin-bottom: 0.5rem;
            font-size: 1.8rem;
        }

        .login-container p {
            color: #666;
            margin-bottom: 2rem;
        }

        .form-group {
            margin-bottom: 1.5rem;
            text-align: left;
        }

        .form-group label {
            display: block;
            margin-bottom: 0.5rem;
            color: var(--dark);
            font-weight: 600;
        }

        .form-group input, .form-group select, .form-group textarea {
            width: 100%;
            padding: 12px 15px;
            border: 2px solid #e0e0e0;
            border-radius: 10px;
            font-size: 1rem;
            transition: border-color 0.3s;
            font-family: inherit;
        }

        .form-group input:focus, .form-group select:focus, .form-group textarea:focus {
            outline: none;
            border-color: var(--primary);
        }

        .btn {
            width: 100%;
            padding: 14px;
            background: var(--primary);
            color: white;
            border: none;
            border-radius: 10px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
        }

        .btn:hover {
            background: var(--secondary);
            transform: translateY(-2px);
            box-shadow: 0 5px 20px rgba(0,102,204,0.4);
        }

        /* Main App Layout */
        .app-container {
            display: none;
            min-height: 100vh;
        }

        .app-container.active {
            display: flex;
        }

        /* Sidebar */
        .sidebar {
            width: var(--sidebar-width);
            background: var(--dark);
            color: white;
            position: fixed;
            height: 100vh;
            overflow-y: auto;
            transition: transform 0.3s;
            z-index: 1000;
        }

        .sidebar-header {
            padding: 2rem;
            background: rgba(0,0,0,0.2);
            text-align: center;
            border-bottom: 1px solid rgba(255,255,255,0.1);
        }

        .sidebar-logo {
            font-size: 3rem;
            color: var(--secondary);
            margin-bottom: 0.5rem;
        }

        .sidebar-header h2 {
            font-size: 1.3rem;
            font-weight: 600;
        }

        .sidebar-header p {
            font-size: 0.9rem;
            opacity: 0.7;
            margin-top: 0.3rem;
        }

        .nav-menu {
            padding: 1rem 0;
        }

        .nav-item {
            padding: 1rem 1.5rem;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 1rem;
            transition: all 0.3s;
            border-left: 4px solid transparent;
        }

        .nav-item:hover, .nav-item.active {
            background: rgba(255,255,255,0.05);
            border-left-color: var(--secondary);
        }

        .nav-item i {
            font-size: 1.2rem;
            width: 30px;
            text-align: center;
        }

        .nav-item span {
            font-weight: 500;
        }

        .sidebar-footer {
            padding: 1.5rem;
            border-top: 1px solid rgba(255,255,255,0.1);
            position: absolute;
            bottom: 0;
            width: 100%;
        }

        .user-info {
            display: flex;
            align-items: center;
            gap: 1rem;
        }

        .user-avatar {
            width: 45px;
            height: 45px;
            border-radius: 50%;
            background: var(--secondary);
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            font-size: 1.2rem;
        }

        .user-details p {
            font-size: 0.9rem;
            font-weight: 600;
        }

        .user-details small {
            opacity: 0.7;
            font-size: 0.8rem;
        }

        /* Main Content */
        .main-content {
            margin-left: var(--sidebar-width);
            flex: 1;
            padding: 2rem;
            min-height: 100vh;
        }

        .top-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 2rem;
            background: white;
            padding: 1rem 2rem;
            border-radius: 15px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }

        .page-title h1 {
            color: var(--dark);
            font-size: 1.8rem;
        }

        .page-title p {
            color: #666;
            font-size: 0.9rem;
        }

        .top-actions {
            display: flex;
            gap: 1rem;
            align-items: center;
        }

        .notification-btn {
            position: relative;
            background: var(--light);
            border: none;
            width: 45px;
            height: 45px;
            border-radius: 50%;
            cursor: pointer;
            font-size: 1.2rem;
            color: var(--dark);
            transition: all 0.3s;
        }

        .notification-btn:hover {
            background: var(--primary);
            color: white;
        }

        .badge {
            position: absolute;
            top: -5px;
            right: -5px;
            background: var(--danger);
            color: white;
            font-size: 0.7rem;
            padding: 2px 6px;
            border-radius: 10px;
            font-weight: bold;
        }

        /* Stats Cards */
        .stats-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 1.5rem;
            margin-bottom: 2rem;
        }

        .stat-card {
            background: white;
            padding: 1.5rem;
            border-radius: 15px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
            display: flex;
            align-items: center;
            gap: 1.5rem;
            transition: transform 0.3s;
        }

        .stat-card:hover {
            transform: translateY(-5px);
        }

        .stat-icon {
            width: 60px;
            height: 60px;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.5rem;
        }

        .stat-icon.blue { background: rgba(0,102,204,0.1); color: var(--primary); }
        .stat-icon.green { background: rgba(40,167,69,0.1); color: var(--success); }
        .stat-icon.orange { background: rgba(255,193,7,0.1); color: var(--warning); }
        .stat-icon.red { background: rgba(220,53,69,0.1); color: var(--danger); }

        .stat-info h3 {
            font-size: 1.8rem;
            color: var(--dark);
            margin-bottom: 0.3rem;
        }

        .stat-info p {
            color: #666;
            font-size: 0.9rem;
        }

        /* Content Sections */
        .content-section {
            display: none;
            animation: fadeIn 0.4s ease;
        }

        .content-section.active {
            display: block;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Cards */
        .card {
            background: white;
            border-radius: 15px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
            overflow: hidden;
            margin-bottom: 1.5rem;
        }

        .card-header {
            padding: 1.5rem;
            border-bottom: 1px solid #eee;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .card-header h3 {
            color: var(--dark);
            font-size: 1.2rem;
        }

        .card-body {
            padding: 1.5rem;
        }

        /* Tables */
        .table-container {
            overflow-x: auto;
        }

        table {
            width: 100%;
            border-collapse: collapse;
        }

        th, td {
            padding: 1rem;
            text-align: left;
            border-bottom: 1px solid #eee;
        }

        th {
            background: var(--light);
            font-weight: 600;
            color: var(--dark);
            font-size: 0.9rem;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        tr:hover {
            background: rgba(0,102,204,0.02);
        }

        .status-badge {
            padding: 5px 12px;
            border-radius: 20px;
            font-size: 0.8rem;
            font-weight: 600;
        }

        .status-paid { background: rgba(40,167,69,0.1); color: var(--success); }
        .status-pending { background: rgba(255,193,7,0.1); color: #856404; }
        .status-overdue { background: rgba(220,53,69,0.1); color: var(--danger); }

        .btn-small {
            padding: 6px 12px;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-size: 0.85rem;
            transition: all 0.3s;
            display: inline-flex;
            align-items: center;
            gap: 5px;
        }

        .btn-view { background: var(--primary); color: white; }
        .btn-edit { background: var(--warning); color: var(--dark); }
        .btn-delete { background: var(--danger); color: white; }

        .btn-small:hover {
            transform: scale(1.05);
            opacity: 0.9;
        }

        /* Map Container - CORREGIDO */
        #map {
            height: 500px;
            width: 100%;
            border-radius: 10px;
            z-index: 1;
        }

        /* Asegurar que Leaflet se muestre correctamente */
        .leaflet-container {
            height: 100%;
            width: 100%;
        }

        .map-controls {
            display: flex;
            gap: 1rem;
            margin-bottom: 1rem;
            flex-wrap: wrap;
        }

        .map-filter {
            display: flex;
            align-items: center;
            gap: 0.5rem;
            background: white;
            padding: 0.5rem 1rem;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }

        .map-filter input[type="checkbox"] {
            width: 18px;
            height: 18px;
            cursor: pointer;
        }

        /* Charts Placeholder */
        .chart-container {
            height: 300px;
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            flex-direction: column;
            color: #666;
        }

        .chart-container i {
            font-size: 3rem;
            margin-bottom: 1rem;
            color: var(--primary);
        }

        /* Modal */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.5);
            z-index: 2000;
            justify-content: center;
            align-items: center;
        }

        .modal.active {
            display: flex;
        }

        .modal-content {
            background: white;
            width: 90%;
            max-width: 600px;
            border-radius: 20px;
            max-height: 90vh;
            overflow-y: auto;
            animation: slideUp 0.3s ease;
        }

        .modal-header {
            padding: 1.5rem;
            border-bottom: 1px solid #eee;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .modal-header h2 {
            color: var(--dark);
        }

        .close-btn {
            background: none;
            border: none;
            font-size: 1.5rem;
            cursor: pointer;
            color: #666;
            width: 40px;
            height: 40px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.3s;
        }

        .close-btn:hover {
            background: var(--light);
            color: var(--danger);
        }

        .modal-body {
            padding: 1.5rem;
        }

        .form-row {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 1rem;
        }

        @media (max-width: 768px) {
            .sidebar {
                transform: translateX(-100%);
            }
            
            .sidebar.active {
                transform: translateX(0);
            }
            
            .main-content {
                margin-left: 0;
            }
            
            .stats-grid {
                grid-template-columns: 1fr;
            }
            
            .form-row {
                grid-template-columns: 1fr;
            }
        }

        /* Quick Actions */
        .quick-actions {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 1rem;
            margin-bottom: 2rem;
        }

        .quick-action-btn {
            background: white;
            border: 2px solid transparent;
            padding: 1.5rem;
            border-radius: 15px;
            cursor: pointer;
            transition: all 0.3s;
            text-align: center;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
        }

        .quick-action-btn:hover {
            border-color: var(--primary);
            transform: translateY(-3px);
        }

        .quick-action-btn i {
            font-size: 2rem;
            color: var(--primary);
            margin-bottom: 0.5rem;
        }

        .quick-action-btn h4 {
            color: var(--dark);
            margin-bottom: 0.3rem;
        }

        .quick-action-btn p {
            font-size: 0.85rem;
            color: #666;
        }

        /* Alert Box */
        .alert {
            padding: 1rem 1.5rem;
            border-radius: 10px;
            margin-bottom: 1.5rem;
            display: flex;
            align-items: center;
            gap: 1rem;
        }

        .alert-warning {
            background: rgba(255,193,7,0.1);
            border-left: 4px solid var(--warning);
            color: #856404;
        }

        .alert i {
            font-size: 1.2rem;
        }

        /* Progress Bar */
        .progress-container {
            margin: 1rem 0;
        }

        .progress-header {
            display: flex;
            justify-content: space-between;
            margin-bottom: 0.5rem;
            font-size: 0.9rem;
        }

        .progress-bar {
            height: 8px;
            background: #e0e0e0;
            border-radius: 4px;
            overflow: hidden;
        }

        .progress-fill {
            height: 100%;
            background: var(--primary);
            border-radius: 4px;
            transition: width 0.5s ease;
        }

        /* Mobile Menu Toggle */
        .menu-toggle {
            display: none;
            position: fixed;
            top: 1rem;
            left: 1rem;
            z-index: 1001;
            background: var(--dark);
            color: white;
            border: none;
            width: 45px;
            height: 45px;
            border-radius: 10px;
            font-size: 1.2rem;
            cursor: pointer;
        }

        @media (max-width: 768px) {
            .menu-toggle {
                display: block;
            }
        }

        /* Custom Leaflet Icons */
        .custom-div-icon {
            background: transparent;
            border: none;
        }
    </style>
</head>
<body>

    <!-- Login Screen -->
    <div class="login-screen" id="loginScreen">
        <div class="login-container">
            <div class="login-logo">
                <i class="fas fa-water"></i>
            </div>
            <h1>AquaManager</h1>
            <p>Sistema de Gestión para Asociaciones de Agua Potable</p>
            
            <form id="loginForm">
                <div class="form-group">
                    <label><i class="fas fa-user"></i> Usuario</label>
                    <input type="text" placeholder="admin" value="admin" required>
                </div>
                <div class="form-group">
                    <label><i class="fas fa-lock"></i> Contraseña</label>
                    <input type="password" placeholder="••••••" value="admin123" required>
                </div>
                <button type="submit" class="btn">
                    <i class="fas fa-sign-in-alt"></i> Ingresar al Sistema
                </button>
            </form>
            
            <p style="margin-top: 1.5rem; font-size: 0.85rem; color: #999;">
                <i class="fas fa-info-circle"></i> Demo: admin / admin123
            </p>
        </div>
    </div>

    <!-- Main Application -->
    <div class="app-container" id="appContainer">
        <button class="menu-toggle" onclick="toggleSidebar()">
            <i class="fas fa-bars"></i>
        </button>

        <!-- Sidebar -->
        <aside class="sidebar" id="sidebar">
            <div class="sidebar-header">
                <div class="sidebar-logo">
                    <i class="fas fa-water"></i>
                </div>
                <h2>AquaManager</h2>
                <p>APR "Los Ángeles"</p>
            </div>

            <nav class="nav-menu">
                <div class="nav-item active" onclick="showSection('dashboard')">
                    <i class="fas fa-home"></i>
                    <span>Dashboard</span>
                </div>
                <div class="nav-item" onclick="showSection('socios')">
                    <i class="fas fa-users"></i>
                    <span>Gestión de Socios</span>
                </div>
                <div class="nav-item" onclick="showSection('pagos')">
                    <i class="fas fa-file-invoice-dollar"></i>
                    <span>Pagos y Facturación</span>
                </div>
                <div class="nav-item" onclick="showSection('mapa')">
                    <i class="fas fa-map-marked-alt"></i>
                    <span>Mapa de Red de Agua</span>
                </div>
                <div class="nav-item" onclick="showSection('averias')">
                    <i class="fas fa-tools"></i>
                    <span>Reporte de Averías</span>
                </div>
                <div class="nav-item" onclick="showSection('mediciones')">
                    <i class="fas fa-tachometer-alt"></i>
                    <span>Medición de Consumo</span>
                </div>
                <div class="nav-item" onclick="showSection('reportes')">
                    <i class="fas fa-chart-bar"></i>
                    <span>Reportes y Estadísticas</span>
                </div>
                <div class="nav-item" onclick="showSection('config')">
                    <i class="fas fa-cog"></i>
                    <span>Configuración</span>
                </div>
            </nav>

            <div class="sidebar-footer">
                <div class="user-info">
                    <div class="user-avatar">AD</div>
                    <div class="user-details">
                        <p>Admin APR</p>
                        <small>admin@apr-losangeles.cl</small>
                    </div>
                </div>
            </div>
        </aside>

        <!-- Main Content -->
        <main class="main-content">
            <!-- Top Bar -->
            <div class="top-bar">
                <div class="page-title">
                    <h1 id="pageTitle">Dashboard</h1>
                    <p id="pageSubtitle">Bienvenido al sistema de gestión</p>
                </div>
                <div class="top-actions">
                    <button class="notification-btn" onclick="showNotifications()">
                        <i class="fas fa-bell"></i>
                        <span class="badge">3</span>
                    </button>
                    <button class="notification-btn" onclick="logout()">
                        <i class="fas fa-sign-out-alt"></i>
                    </button>
                </div>
            </div>

            <!-- DASHBOARD SECTION -->
            <section id="dashboard" class="content-section active">
                <div class="alert alert-warning">
                    <i class="fas fa-exclamation-triangle"></i>
                    <div>
                        <strong>Atención:</strong> 12 socios tienen pagos pendientes por más de 30 días. 
                        <a href="#" onclick="showSection('pagos')" style="color: inherit; text-decoration: underline;">Ver detalles</a>
                    </div>
                </div>

                <!-- Stats Grid -->
                <div class="stats-grid">
                    <div class="stat-card">
                        <div class="stat-icon blue">
                            <i class="fas fa-users"></i>
                        </div>
                        <div class="stat-info">
                            <h3>248</h3>
                            <p>Socios Activos</p>
                        </div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-icon green">
                            <i class="fas fa-check-circle"></i>
                        </div>
                        <div class="stat-info">
                            <h3>92%</h3>
                            <p>Cobranza del Mes</p>
                        </div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-icon orange">
                            <i class="fas fa-exclamation-circle"></i>
                        </div>
                        <div class="stat-info">
                            <h3>5</h3>
                            <p>Averías Pendientes</p>
                        </div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-icon red">
                            <i class="fas fa-dollar-sign"></i>
                        </div>
                        <div class="stat-info">
                            <h3>$1.2M</h3>
                            <p>Deuda Total</p>
                        </div>
                    </div>
                </div>

                <!-- Quick Actions -->
                <div class="quick-actions">
                    <div class="quick-action-btn" onclick="openModal('nuevoSocio')">
                        <i class="fas fa-user-plus"></i>
                        <h4>Nuevo Socio</h4>
                        <p>Registrar un nuevo miembro</p>
                    </div>
                    <div class="quick-action-btn" onclick="openModal('registrarPago')">
                        <i class="fas fa-money-bill-wave"></i>
                        <h4>Registrar Pago</h4>
                        <p>Agregar pago de un socio</p>
                    </div>
                    <div class="quick-action-btn" onclick="openModal('reportarAveria')">
                        <i class="fas fa-wrench"></i>
                        <h4>Reportar Avería</h4>
                        <p>Informar problema en la red</p>
                    </div>
                    <div class="quick-action-btn" onclick="showSection('mediciones')">
                        <i class="fas fa-clipboard-list"></i>
                        <h4>Ingresar Lectura</h4>
                        <p>Registrar medición mensual</p>
                    </div>
                </div>

                <div class="card">
                    <div class="card-header">
                        <h3><i class="fas fa-bell"></i> Últimas Actividades</h3>
                        <button class="btn-small btn-view">Ver Todo</button>
                    </div>
                    <div class="card-body">
                        <div class="table-container">
                            <table>
                                <thead>
                                    <tr>
                                        <th>Fecha</th>
                                        <th>Actividad</th>
                                        <th>Usuario</th>
                                        <th>Estado</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr>
                                        <td>26 Feb 2025</td>
                                        <td>Pago registrado - Socio #1245</td>
                                        <td>Admin</td>
                                        <td><span class="status-badge status-paid">Completado</span></td>
                                    </tr>
                                    <tr>
                                        <td>26 Feb 2025</td>
                                        <td>Avería reportada - Sector Norte</td>
                                        <td>Juan Pérez</td>
                                        <td><span class="status-badge status-pending">En Proceso</span></td>
                                    </tr>
                                    <tr>
                                        <td>25 Feb 2025</td>
                                        <td>Nuevo socio registrado</td>
                                        <td>Admin</td>
                                        <td><span class="status-badge status-paid">Completado</span></td>
                                    </tr>
                                    <tr>
                                        <td>25 Feb 2025</td>
                                        <td>Lecturas mensuales ingresadas</td>
                                        <td>Maria González</td>
                                        <td><span class="status-badge status-paid">Completado</span></td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </section>

            <!-- SOCIOS SECTION -->
            <section id="socios" class="content-section">
                <div class="card">
                    <div class="card-header">
                        <h3><i class="fas fa-users"></i> Listado de Socios</h3>
                        <div style="display: flex; gap: 0.5rem;">
                            <button class="btn-small btn-view" onclick="openModal('nuevoSocio')">
                                <i class="fas fa-plus"></i> Nuevo
                            </button>
                            <button class="btn-small btn-edit">
                                <i class="fas fa-file-export"></i> Exportar
                            </button>
                        </div>
                    </div>
                    <div class="card-body">
                        <div style="display: flex; gap: 1rem; margin-bottom: 1rem;">
                            <input type="text" placeholder="Buscar socio..." style="flex: 1; padding: 10px; border: 1px solid #ddd; border-radius: 8px;">
                            <select style="padding: 10px; border: 1px solid #ddd; border-radius: 8px;">
                                <option>Todos los estados</option>
                                <option>Activo</option>
                                <option>Moroso</option>
                                <option>Suspendido</option>
                            </select>
                        </div>
                        <div class="table-container">
                            <table>
                                <thead>
                                    <tr>
                                        <th>N° Socio</th>
                                        <th>Nombre</th>
                                        <th>Dirección</th>
                                        <th>Medidor</th>
                                        <th>Estado</th>
                                        <th>Acciones</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr>
                                        <td>001</td>
                                        <td>Juan Pérez González</td>
                                        <td>Calle Principal 123</td>
                                        <td>M-1245</td>
                                        <td><span class="status-badge status-paid">Al día</span></td>
                                        <td>
                                            <button class="btn-small btn-view"><i class="fas fa-eye"></i></button>
                                            <button class="btn-small btn-edit"><i class="fas fa-edit"></i></button>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>002</td>
                                        <td>María González Silva</td>
                                        <td>Av. Los Pinos 45</td>
                                        <td>M-1246</td>
                                        <td><span class="status-badge status-paid">Al día</span></td>
                                        <td>
                                            <button class="btn-small btn-view"><i class="fas fa-eye"></i></button>
                                            <button class="btn-small btn-edit"><i class="fas fa-edit"></i></button>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>003</td>
                                        <td>Carlos Rodríguez López</td>
                                        <td>Camino Rural 78</td>
                                        <td>M-1247</td>
                                        <td><span class="status-badge status-overdue">3 meses adeudado</span></td>
                                        <td>
                                            <button class="btn-small btn-view"><i class="fas fa-eye"></i></button>
                                            <button class="btn-small btn-edit"><i class="fas fa-edit"></i></button>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>004</td>
                                        <td>Ana María Soto</td>
                                        <td>Los Robles 234</td>
                                        <td>M-1248</td>
                                        <td><span class="status-badge status-pending">Pago pendiente</span></td>
                                        <td>
                                            <button class="btn-small btn-view"><i class="fas fa-eye"></i></button>
                                            <button class="btn-small btn-edit"><i class="fas fa-edit"></i></button>
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </section>

            <!-- PAGOS SECTION -->
            <section id="pagos" class="content-section">
                <div class="stats-grid" style="grid-template-columns: repeat(3, 1fr);">
                    <div class="stat-card">
                        <div class="stat-icon green">
                            <i class="fas fa-check"></i>
                        </div>
                        <div class="stat-info">
                            <h3>$2.4M</h3>
                            <p>Recaudado Febrero</p>
                        </div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-icon orange">
                            <i class="fas fa-clock"></i>
                        </div>
                        <div class="stat-info">
                            <h3>$380K</h3>
                            <p>Pendiente de Cobro</p>
                        </div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-icon red">
                            <i class="fas fa-ban"></i>
                        </div>
                        <div class="stat-info">
                            <h3>$1.2M</h3>
                            <p>Morosidad > 90 días</p>
                        </div>
                    </div>
                </div>

                <div class="card">
                    <div class="card-header">
                        <h3><i class="fas fa-file-invoice-dollar"></i> Estado de Pagos - Febrero 2025</h3>
                        <button class="btn-small btn-view" onclick="openModal('registrarPago')">
                            <i class="fas fa-plus"></i> Registrar Pago
                        </button>
                    </div>
                    <div class="card-body">
                        <div class="table-container">
                            <table>
                                <thead>
                                    <tr>
                                        <th>Socio</th>
                                        <th>Mes</th>
                                        <th>Monto</th>
                                        <th>Fecha Venc.</th>
                                        <th>Estado</th>
                                        <th>Acciones</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr>
                                        <td>Juan Pérez G.</td>
                                        <td>Febrero 2025</td>
                                        <td>$15.000</td>
                                        <td>15/02/2025</td>
                                        <td><span class="status-badge status-paid">Pagado</span></td>
                                        <td>
                                            <button class="btn-small btn-view"><i class="fas fa-file-alt"></i> Boleta</button>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>María González S.</td>
                                        <td>Febrero 2025</td>
                                        <td>$15.000</td>
                                        <td>15/02/2025</td>
                                        <td><span class="status-badge status-paid">Pagado</span></td>
                                        <td>
                                            <button class="btn-small btn-view"><i class="fas fa-file-alt"></i> Boleta</button>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>Carlos Rodríguez L.</td>
                                        <td>Febrero 2025</td>
                                        <td>$15.000</td>
                                        <td>15/02/2025</td>
                                        <td><span class="status-badge status-overdue">Vencido</span></td>
                                        <td>
                                            <button class="btn-small btn-view"><i class="fas fa-money-bill"></i> Cobrar</button>
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </section>

            <!-- MAPA SECTION - CORREGIDO -->
            <section id="mapa" class="content-section">
                <div class="card">
                    <div class="card-header">
                        <h3><i class="fas fa-map-marked-alt"></i> Infraestructura de Agua Potable</h3>
                    </div>
                    <div class="card-body">
                        <div class="map-controls">
                            <div class="map-filter">
                                <input type="checkbox" checked id="showTanks">
                                <label for="showTanks">Estanques</label>
                            </div>
                            <div class="map-filter">
                                <input type="checkbox" checked id="showPipes">
                                <label for="showPipes">Tuberías</label>
                            </div>
                            <div class="map-filter">
                                <input type="checkbox" checked id="showHouses">
                                <label for="showHouses">Conexiones</label>
                            </div>
                            <div class="map-filter">
                                <input type="checkbox" id="showAverias">
                                <label for="showAverias">Mostrar Averías</label>
                            </div>
                        </div>
                        <div id="map"></div>
                    </div>
                </div>

                <div class="stats-grid" style="margin-top: 1.5rem;">
                    <div class="stat-card">
                        <div class="stat-icon blue">
                            <i class="fas fa-database"></i>
                        </div>
                        <div class="stat-info">
                            <h3>3</h3>
                            <p>Estanques de Almacenamiento</p>
                        </div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-icon blue">
                            <i class="fas fa-network-wired"></i>
                        </div>
                        <div class="stat-info">
                            <h3>12.5 km</h3>
                            <p>Red de Tuberías</p>
                        </div>
                    </div>
                    <div class="stat-card">
                        <div class="stat-icon blue">
                            <i class="fas fa-home"></i>
                        </div>
                        <div class="stat-info">
                            <h3>248</h3>
                            <p>Conexiones Activas</p>
                        </div>
                    </div>
                </div>
            </section>

            <!-- AVERIAS SECTION -->
            <section id="averias" class="content-section">
                <div class="card">
                    <div class="card-header">
                        <h3><i class="fas fa-tools"></i> Gestión de Averías</h3>
                        <button class="btn-small btn-view" onclick="openModal('reportarAveria')">
                            <i class="fas fa-plus"></i> Reportar Avería
                        </button>
                    </div>
                    <div class="card-body">
                        <div class="table-container">
                            <table>
                                <thead>
                                    <tr>
                                        <th>ID</th>
                                        <th>Fecha</th>
                                        <th>Ubicación</th>
                                        <th>Tipo</th>
                                        <th>Prioridad</th>
                                        <th>Estado</th>
                                        <th>Acciones</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr>
                                        <td>AV-2025-001</td>
                                        <td>25/02/2025</td>
                                        <td>Sector Norte - Calle Principal</td>
                                        <td>Fuga en tubería principal</td>
                                        <td><span class="status-badge status-overdue">Alta</span></td>
                                        <td><span class="status-badge status-pending">En Reparación</span></td>
                                        <td>
                                            <button class="btn-small btn-view"><i class="fas fa-eye"></i></button>
                                            <button class="btn-small btn-edit"><i class="fas fa-check"></i> Cerrar</button>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>AV-2025-002</td>
                                        <td>24/02/2025</td>
                                        <td>Estanque 2 - Válvula de ingreso</td>
                                        <td>Válvula dañada</td>
                                        <td><span class="status-badge status-pending">Media</span></td>
                                        <td><span class="status-badge status-pending">Pendiente</span></td>
                                        <td>
                                            <button class="btn-small btn-view"><i class="fas fa-eye"></i></button>
                                            <button class="btn-small btn-edit"><i class="fas fa-play"></i> Iniciar</button>
                                        </td>
                                    </tr>
                                    <tr>
                                        <td>AV-2025-003</td>
                                        <td>20/02/2025</td>
                                        <td>Calle Los Pinos 45</td>
                                        <td>Conexión domiciliaria rota</td>
                                        <td><span class="status-badge status-paid">Baja</span></td>
                                        <td><span class="status-badge status-paid">Reparado</span></td>
                                        <td>
                                            <button class="btn-small btn-view"><i class="fas fa-eye"></i></button>
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </section>

            <!-- MEDICIONES SECTION -->
            <section id="mediciones" class="content-section">
                <div class="card">
                    <div class="card-header">
                        <h3><i class="fas fa-tachometer-alt"></i> Registro de Lecturas - Febrero 2025</h3>
                        <div style="display: flex; gap: 0.5rem;">
                            <button class="btn-small btn-view">
                                <i class="fas fa-upload"></i> Importar
                            </button>
                            <button class="btn-small btn-edit">
                                <i class="fas fa-save"></i> Guardar
                            </button>
                        </div>
                    </div>
                    <div class="card-body">
                        <div class="progress-container">
                            <div class="progress-header">
                                <span>Progreso de lecturas</span>
                                <span>85% (211/248)</span>
                            </div>
                            <div class="progress-bar">
                                <div class="progress-fill" style="width: 85%"></div>
                            </div>
                        </div>
                        
                        <div class="table-container">
                            <table>
                                <thead>
                                    <tr>
                                        <th>Socio</th>
                                        <th>Medidor</th>
                                        <th>Lectura Anterior</th>
                                        <th>Lectura Actual</th>
                                        <th>Consumo (m³)</th>
                                        <th>Estado</th>
                                    </tr>
                                </thead>
                                <tbody>
                                    <tr>
                                        <td>Juan Pérez G.</td>
                                        <td>M-1245</td>
                                        <td>1,245</td>
                                        <td><input type="number" value="1258" style="width: 100px; padding: 5px;"></td>
                                        <td>13</td>
                                        <td><span class="status-badge status-paid">Ingresado</span></td>
                                    </tr>
                                    <tr>
                                        <td>María González S.</td>
                                        <td>M-1246</td>
                                        <td>892</td>
                                        <td><input type="number" value="903" style="width: 100px; padding: 5px;"></td>
                                        <td>11</td>
                                        <td><span class="status-badge status-paid">Ingresado</span></td>
                                    </tr>
                                    <tr>
                                        <td>Carlos Rodríguez L.</td>
                                        <td>M-1247</td>
                                        <td>2,156</td>
                                        <td><input type="number" placeholder="Ingresar" style="width: 100px; padding: 5px;"></td>
                                        <td>-</td>
                                        <td><span class="status-badge status-pending">Pendiente</span></td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </section>

            <!-- REPORTES SECTION -->
            <section id="reportes" class="content-section">
                <div class="stats-grid">
                    <div class="card">
                        <div class="card-header">
                            <h3>Consumo Mensual (m³)</h3>
                        </div>
                        <div class="card-body">
                            <div class="chart-container">
                                <i class="fas fa-chart-bar"></i>
                                <p>Gráfico de Consumo Mensual</p>
                                <small>Enero: 3,240 m³ | Febrero: 3,180 m³</small>
                            </div>
                        </div>
                    </div>
                    <div class="card">
                        <div class="card-header">
                            <h3>Cobranza Histórica</h3>
                        </div>
                        <div class="card-body">
                            <div class="chart-container">
                                <i class="fas fa-chart-line"></i>
                                <p>Tendencia de Cobranza</p>
                                <small>Promedio 2024: 94% cobranza</small>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="card">
                    <div class="card-header">
                        <h3><i class="fas fa-file-pdf"></i> Reportes Disponibles</h3>
                    </div>
                    <div class="card-body">
                        <div class="quick-actions">
                            <div class="quick-action-btn" style="text-align: left; display: flex; align-items: center; gap: 1rem;">
                                <i class="fas fa-file-invoice" style="margin: 0;"></i>
                                <div>
                                    <h4>Estado de Pagos</h4>
                                    <p>Listado completo de deudas por socio</p>
                                </div>
                            </div>
                            <div class="quick-action-btn" style="text-align: left; display: flex; align-items: center; gap: 1rem;">
                                <i class="fas fa-tint" style="margin: 0;"></i>
                                <div>
                                    <h4>Consumo por Sector</h4>
                                    <p>Análisis de consumo por zonas</p>
                                </div>
                            </div>
                            <div class="quick-action-btn" style="text-align: left; display: flex; align-items: center; gap: 1rem;">
                                <i class="fas fa-tools" style="margin: 0;"></i>
                                <div>
                                    <h4>Mantenimientos</h4>
                                    <p>Historial de reparaciones</p>
                                </div>
                            </div>
                            <div class="quick-action-btn" style="text-align: left; display: flex; align-items: center; gap: 1rem;">
                                <i class="fas fa-users" style="margin: 0;"></i>
                                <div>
                                    <h4>Padrón de Socios</h4>
                                    <p>Listado completo actualizado</p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </section>

            <!-- CONFIG SECTION -->
            <section id="config" class="content-section">
                <div class="card">
                    <div class="card-header">
                        <h3><i class="fas fa-cog"></i> Configuración del Sistema</h3>
                    </div>
                    <div class="card-body">
                        <div class="form-row">
                            <div class="form-group">
                                <label>Nombre de la APR</label>
                                <input type="text" value="Asociación de Agua Potable Rural Los Ángeles">
                            </div>
                            <div class="form-group">
                                <label>RUT</label>
                                <input type="text" value="70.123.456-7">
                            </div>
                        </div>
                        <div class="form-row">
                            <div class="form-group">
                                <label>Dirección</label>
                                <input type="text" value="Calle Principal S/N, Sector Rural">
                            </div>
                            <div class="form-group">
                                <label>Teléfono</label>
                                <input type="text" value="+56 9 1234 5678">
                            </div>
                        </div>
                        <div class="form-row">
                            <div class="form-group">
                                <label>Valor del Metro Cúbico</label>
                                <input type="number" value="850">
                            </div>
                            <div class="form-group">
                                <label>Cargo Fijo Mensual</label>
                                <input type="number" value="5000">
                            </div>
                        </div>
                        <div class="form-row">
                            <div class="form-group">
                                <label>Día de Vencimiento</label>
                                <input type="number" value="15">
                            </div>
                            <div class="form-group">
                                <label>Interés por Mora (%)</label>
                                <input type="number" value="2">
                            </div>
                        </div>
                        <button class="btn" style="width: auto; margin-top: 1rem;">
                            <i class="fas fa-save"></i> Guardar Configuración
                        </button>
                    </div>
                </div>
            </section>
        </main>
    </div>

    <!-- Modals -->
    <div class="modal" id="modal">
        <div class="modal-content">
            <div class="modal-header">
                <h2 id="modalTitle">Título</h2>
                <button class="close-btn" onclick="closeModal()">
                    <i class="fas fa-times"></i>
                </button>
            </div>
            <div class="modal-body" id="modalBody">
                <!-- Content loaded dynamically -->
            </div>
        </div>
    </div>

    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
    <script>
        // Login Handler
        document.getElementById('loginForm').addEventListener('submit', function(e) {
            e.preventDefault();
            document.getElementById('loginScreen').classList.add('hidden');
            document.getElementById('appContainer').classList.add('active');
        });

        // Navigation
        function showSection(sectionId) {
            // Hide all sections
            document.querySelectorAll('.content-section').forEach(section => {
                section.classList.remove('active');
            });
            
            // Show target section
            document.getElementById(sectionId).classList.add('active');
            
            // Update nav items
            document.querySelectorAll('.nav-item').forEach(item => {
                item.classList.remove('active');
            });
            
            // Find the clicked nav item and make it active
            const navItems = document.querySelectorAll('.nav-item');
            navItems.forEach(item => {
                if(item.getAttribute('onclick') && item.getAttribute('onclick').includes(sectionId)) {
                    item.classList.add('active');
                }
            });
            
            // Update page title
            const titles = {
                'dashboard': ['Dashboard', 'Panel de control general'],
                'socios': ['Gestión de Socios', 'Administrar miembros de la APR'],
                'pagos': ['Pagos y Facturación', 'Control de cobranzas'],
                'mapa': ['Mapa de Red de Agua', 'Infraestructura GIS'],
                'averias': ['Reporte de Averías', 'Gestión de mantenimiento'],
                'mediciones': ['Medición de Consumo', 'Registro de lecturas'],
                'reportes': ['Reportes', 'Estadísticas y análisis'],
                'config': ['Configuración', 'Ajustes del sistema']
            };
            
            document.getElementById('pageTitle').textContent = titles[sectionId][0];
            document.getElementById('pageSubtitle').textContent = titles[sectionId][1];
            
            // Initialize map if showing map section
            if (sectionId === 'mapa') {
                setTimeout(initMap, 100);
            }
            
            // Close sidebar on mobile
            if (window.innerWidth <= 768) {
                document.getElementById('sidebar').classList.remove('active');
            }
        }

        // Sidebar Toggle
        function toggleSidebar() {
            document.getElementById('sidebar').classList.toggle('active');
        }

        // Modal Functions
        function openModal(type) {
            const modal = document.getElementById('modal');
            const title = document.getElementById('modalTitle');
            const body = document.getElementById('modalBody');
            
            const contents = {
                'nuevoSocio': {
                    title: 'Registrar Nuevo Socio',
                    html: `
                        <div class="form-row">
                            <div class="form-group">
                                <label>Nombres</label>
                                <input type="text" placeholder="Ej: Juan Carlos">
                            </div>
                            <div class="form-group">
                                <label>Apellidos</label>
                                <input type="text" placeholder="Ej: Pérez González">
                            </div>
                        </div>
                        <div class="form-group">
                            <label>RUT</label>
                            <input type="text" placeholder="12.345.678-9">
                        </div>
                        <div class="form-group">
                            <label>Dirección</label>
                            <input type="text" placeholder="Calle, número, sector">
                        </div>
                        <div class="form-row">
                            <div class="form-group">
                                <label>Teléfono</label>
                                <input type="tel" placeholder="+56 9 1234 5678">
                            </div>
                            <div class="form-group">
                                <label>Email</label>
                                <input type="email" placeholder="correo@ejemplo.com">
                            </div>
                        </div>
                        <div class="form-row">
                            <div class="form-group">
                                <label>N° Medidor</label>
                                <input type="text" placeholder="M-XXXX">
                            </div>
                            <div class="form-group">
                                <label>Fecha Conexión</label>
                                <input type="date">
                            </div>
                        </div>
                        <button class="btn" onclick="closeModal()">
                            <i class="fas fa-save"></i> Guardar Socio
                        </button>
                    `
                },
                'registrarPago': {
                    title: 'Registrar Pago',
                    html: `
                        <div class="form-group">
                            <label>Buscar Socio</label>
                            <input type="text" placeholder="Nombre o número de socio...">
                        </div>
                        <div class="form-row">
                            <div class="form-group">
                                <label>Mes a Pagar</label>
                                <select>
                                    <option>Febrero 2025</option>
                                    <option>Enero 2025</option>
                                    <option>Diciembre 2024</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <label>Monto</label>
                                <input type="number" value="15000">
                            </div>
                        </div>
                        <div class="form-group">
                            <label>Método de Pago</label>
                            <select>
                                <option>Efectivo</option>
                                <option>Transferencia</option>
                                <option>Depósito</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <label>Observaciones</label>
                            <input type="text" placeholder="Opcional">
                        </div>
                        <button class="btn" onclick="closeModal()">
                            <i class="fas fa-check"></i> Confirmar Pago
                        </button>
                    `
                },
                'reportarAveria': {
                    title: 'Reportar Nueva Avería',
                    html: `
                        <div class="form-group">
                            <label>Ubicación</label>
                            <input type="text" placeholder="Dirección o referencia del lugar">
                        </div>
                        <div class="form-group">
                            <label>Tipo de Avería</label>
                            <select>
                                <option>Fuga en tubería</option>
                                <option>Válvula dañada</option>
                                <option>Conexión rota</option>
                                <option>Estanque</option>
                                <option>Captación</option>
                                <option>Otro</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <label>Prioridad</label>
                            <select>
                                <option>Alta - Sin servicio</option>
                                <option>Media - Servicio intermitente</option>
                                <option>Baja - No afecta servicio</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <label>Descripción</label>
                            <textarea rows="3" placeholder="Describa el problema detalladamente..."></textarea>
                        </div>
                        <button class="btn" onclick="closeModal()">
                            <i class="fas fa-paper-plane"></i> Enviar Reporte
                        </button>
                    `
                }
            };
            
            title.textContent = contents[type].title;
            body.innerHTML = contents[type].html;
            modal.classList.add('active');
        }

        function closeModal() {
            document.getElementById('modal').classList.remove('active');
        }

        // Close modal on outside click
        document.getElementById('modal').addEventListener('click', function(e) {
            if (e.target === this) closeModal();
        });

        // Logout
        function logout() {
            location.reload();
        }

        // Notifications
        function showNotifications() {
            alert('Notificaciones:\n\n• 12 socios con pagos vencidos\n• Avería reportada en Sector Norte\n• Mantenimiento programado: 01/03/2025');
        }

        // Map Initialization - CORREGIDO
        let map;
        let mapInitialized = false;
        
        function initMap() {
            // Evitar reinicialización
            if (mapInitialized) {
                map.invalidateSize();
                return;
            }
            
            // CORRECCIÓN PRINCIPAL: Usar fitBounds correctamente
            // Las coordenadas deben ser [lat, lng] no [lng, lat]
            const bounds = [
                [-36.71100289791769, -72.45649638245762], // Southwest
                [-36.641693503572576, -72.3334395143845]  // Northeast
            ];
            
            // Crear mapa sin setView inicial, luego aplicar fitBounds
            map = L.map('map', {
                zoomControl: true,
                attributionControl: true
            });
            
            // Add base layers
            const osm = L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
                maxZoom: 19,
                attribution: '© OpenStreetMap'
            });
            
            const satellite = L.tileLayer('https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}', {
                maxZoom: 18,
                attribution: '© Esri'
            });
            
            // Add default layer
            satellite.addTo(map);
            
            // Layer control
            const baseLayers = {
                "OpenStreetMap": osm,
                "Satélite": satellite
            };
            
            L.control.layers(baseLayers).addTo(map);
            
            // Add scale control
            L.control.scale().addTo(map);
            
            // CORRECCIÓN: Aplicar fitBounds DESPUÉS de crear el mapa
            map.fitBounds(bounds);
            
            // Sample data points (simulating your GeoJSON data)
            const locations = [
                {lat: -36.676, lng: -72.395, type: 'tank', name: 'Estanque Principal', icon: 'database'},
                {lat: -36.680, lng: -72.400, type: 'tank', name: 'Estanque Secundario', icon: 'database'},
                {lat: -36.675, lng: -72.390, type: 'office', name: 'Oficina APR', icon: 'building'},
                {lat: -36.678, lng: -72.398, type: 'plant', name: 'Planta de Tratamiento', icon: 'industry'},
                {lat: -36.670, lng: -72.385, type: 'connection', name: 'Conexión #1245', icon: 'home'},
                {lat: -36.672, lng: -72.392, type: 'connection', name: 'Conexión #1246', icon: 'home'},
                {lat: -36.685, lng: -72.405, type: 'connection', name: 'Conexión #1247', icon: 'home'},
            ];
            
            // Custom icons
            const icons = {
                tank: L.divIcon({
                    html: '<div style="background: #0066cc; color: white; padding: 8px; border-radius: 50%; width: 35px; height: 35px; display: flex; align-items: center; justify-content: center; box-shadow: 0 2px 5px rgba(0,0,0,0.3);"><i class="fas fa-database"></i></div>',
                    iconSize: [35, 35],
                    className: 'custom-div-icon',
                    iconAnchor: [17, 17]
                }),
                office: L.divIcon({
                    html: '<div style="background: #28a745; color: white; padding: 8px; border-radius: 50%; width: 35px; height: 35px; display: flex; align-items: center; justify-content: center; box-shadow: 0 2px 5px rgba(0,0,0,0.3);"><i class="fas fa-building"></i></div>',
                    iconSize: [35, 35],
                    className: 'custom-div-icon',
                    iconAnchor: [17, 17]
                }),
                plant: L.divIcon({
                    html: '<div style="background: #ffc107; color: #333; padding: 8px; border-radius: 50%; width: 35px; height: 35px; display: flex; align-items: center; justify-content: center; box-shadow: 0 2px 5px rgba(0,0,0,0.3);"><i class="fas fa-industry"></i></div>',
                    iconSize: [35, 35],
                    className: 'custom-div-icon',
                    iconAnchor: [17, 17]
                }),
                connection: L.divIcon({
                    html: '<div style="background: #dc3545; color: white; padding: 6px; border-radius: 50%; width: 28px; height: 28px; display: flex; align-items: center; justify-content: center; box-shadow: 0 2px 5px rgba(0,0,0,0.3); font-size: 0.8rem;"><i class="fas fa-home"></i></div>',
                    iconSize: [28, 28],
                    className: 'custom-div-icon',
                    iconAnchor: [14, 14]
                })
            };
            
            // Add markers
            locations.forEach(loc => {
                const marker = L.marker([loc.lat, loc.lng], {icon: icons[loc.type]})
                    .addTo(map)
                    .bindPopup(`<b>${loc.name}</b><br>Tipo: ${loc.type}<br><a href="#" onclick="alert('Ver detalles de ${loc.name}')">Ver detalles</a>`);
            });
            
            // Add sample pipe line
            const pipeLine = [
                [-36.676, -72.395],
                [-36.678, -72.398],
                [-36.680, -72.400],
                [-36.685, -72.405]
            ];
            
            L.polyline(pipeLine, {
                color: '#0066cc',
                weight: 4,
                opacity: 0.8,
                dashArray: '10, 10'
            }).addTo(map).bindPopup("Tubería Principal Ø110mm");
            
            // CORRECCIÓN IMPORTANTE: Invalidar tamaño después de que el contenedor es visible
            setTimeout(() => {
                map.invalidateSize();
            }, 300);
            
            mapInitialized = true;
        }

        // Initialize with dashboard
        window.onload = function() {
            // Pre-select dashboard nav item
            document.querySelector('.nav-item').classList.add('active');
        };
    </script>
</body>
</html>
