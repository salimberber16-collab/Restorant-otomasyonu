import { useState, useEffect } from "react";

// ─── MOCK DATABASE (SQL Server Simülasyonu) ───────────────────────────────────
const DB_INITIAL = {
  tables: [
    { id: 1, name: "Masa 1", capacity: 2, status: "boş", waiter: null },
    { id: 2, name: "Masa 2", capacity: 4, status: "dolu", waiter: "Ahmet" },
    { id: 3, name: "Masa 3", capacity: 6, status: "dolu", waiter: "Fatma" },
    { id: 4, name: "Masa 4", capacity: 2, status: "rezerve", waiter: null },
    { id: 5, name: "Masa 5", capacity: 4, status: "boş", waiter: null },
    { id: 6, name: "Masa 6", capacity: 8, status: "dolu", waiter: "Mehmet" },
    { id: 7, name: "Masa 7", capacity: 2, status: "boş", waiter: null },
    { id: 8, name: "Masa 8", capacity: 4, status: "dolu", waiter: "Zeynep" },
    { id: 9, name: "Masa 9", capacity: 6, status: "boş", waiter: null },
    { id: 10, name: "Masa 10", capacity: 4, status: "rezerve", waiter: null },
    { id: 11, name: "Masa 11", capacity: 2, status: "dolu", waiter: "Ahmet" },
    { id: 12, name: "Masa 12", capacity: 8, status: "boş", waiter: null },
  ],
  menu: [
    { id: 1, name: "Mercimek Çorbası", category: "Çorba", price: 85, stock: 50 },
    { id: 2, name: "Ezogelin Çorbası", category: "Çorba", price: 85, stock: 45 },
    { id: 3, name: "Domates Çorbası", category: "Çorba", price: 80, stock: 30 },
    { id: 4, name: "Izgara Köfte", category: "Ana Yemek", price: 280, stock: 20 },
    { id: 5, name: "Tavuk Şiş", category: "Ana Yemek", price: 260, stock: 18 },
    { id: 6, name: "Karışık Izgara", category: "Ana Yemek", price: 420, stock: 12 },
    { id: 7, name: "Kuzu Pirzola", category: "Ana Yemek", price: 580, stock: 8 },
    { id: 8, name: "Balık Tava", category: "Balık", price: 380, stock: 15 },
    { id: 9, name: "Levrek Izgara", category: "Balık", price: 480, stock: 10 },
    { id: 10, name: "Çoban Salata", category: "Salata", price: 95, stock: 60 },
    { id: 11, name: "Mevsim Salata", category: "Salata", price: 90, stock: 55 },
    { id: 12, name: "Baklava", category: "Tatlı", price: 120, stock: 25 },
    { id: 13, name: "Künefe", category: "Tatlı", price: 150, stock: 20 },
    { id: 14, name: "Sütlaç", category: "Tatlı", price: 100, stock: 30 },
    { id: 15, name: "Ayran", category: "İçecek", price: 45, stock: 100 },
    { id: 16, name: "Kola", category: "İçecek", price: 55, stock: 80 },
    { id: 17, name: "Su", category: "İçecek", price: 20, stock: 200 },
    { id: 18, name: "Çay", category: "İçecek", price: 30, stock: 150 },
    { id: 19, name: "Türk Kahvesi", category: "İçecek", price: 65, stock: 100 },
    { id: 20, name: "Fıstıklı Baklava", category: "Tatlı", price: 140, stock: 18 },
  ],
  orders: [
    {
      id: 1001, tableId: 2,
      items: [
        { menuId: 1, name: "Mercimek Çorbası", qty: 2, price: 85 },
        { menuId: 4, name: "Izgara Köfte", qty: 2, price: 280 },
        { menuId: 15, name: "Ayran", qty: 2, price: 45 },
      ],
      status: "hazırlanıyor", total: 820, time: "19:30", waiter: "Ahmet",
    },
    {
      id: 1002, tableId: 3,
      items: [
        { menuId: 6, name: "Karışık Izgara", qty: 1, price: 420 },
        { menuId: 10, name: "Çoban Salata", qty: 2, price: 95 },
        { menuId: 13, name: "Künefe", qty: 1, price: 150 },
        { menuId: 16, name: "Kola", qty: 3, price: 55 },
      ],
      status: "teslim edildi", total: 940, time: "19:15", waiter: "Fatma",
    },
    {
      id: 1003, tableId: 6,
      items: [
        { menuId: 7, name: "Kuzu Pirzola", qty: 2, price: 580 },
        { menuId: 9, name: "Levrek Izgara", qty: 2, price: 480 },
        { menuId: 12, name: "Baklava", qty: 4, price: 120 },
      ],
      status: "hazırlanıyor", total: 2600, time: "19:45", waiter: "Mehmet",
    },
    {
      id: 1004, tableId: 8,
      items: [
        { menuId: 5, name: "Tavuk Şiş", qty: 2, price: 260 },
        { menuId: 11, name: "Mevsim Salata", qty: 1, price: 90 },
        { menuId: 18, name: "Çay", qty: 2, price: 30 },
      ],
      status: "bekliyor", total: 670, time: "20:00", waiter: "Zeynep",
    },
    {
      id: 1005, tableId: 11,
      items: [
        { menuId: 2, name: "Ezogelin Çorbası", qty: 2, price: 85 },
        { menuId: 5, name: "Tavuk Şiş", qty: 1, price: 260 },
        { menuId: 19, name: "Türk Kahvesi", qty: 2, price: 65 },
      ],
      status: "bekliyor", total: 560, time: "20:10", waiter: "Ahmet",
    },
  ],
  staff: [
    { id: 1, name: "Ahmet Yılmaz", role: "Garson", status: "aktif", phone: "0532 111 2233", tables: [2, 11] },
    { id: 2, name: "Fatma Kaya", role: "Garson", status: "aktif", phone: "0533 222 3344", tables: [3] },
    { id: 3, name: "Mehmet Demir", role: "Garson", status: "aktif", phone: "0534 333 4455", tables: [6] },
    { id: 4, name: "Zeynep Çelik", role: "Garson", status: "aktif", phone: "0535 444 5566", tables: [8] },
    { id: 5, name: "Ali Şahin", role: "Aşçı", status: "aktif", phone: "0536 555 6677", tables: [] },
    { id: 6, name: "Ayşe Arslan", role: "Kasiyer", status: "aktif", phone: "0537 666 7788", tables: [] },
    { id: 7, name: "Mustafa Öztürk", role: "Aşçı", status: "aktif", phone: "0538 777 8899", tables: [] },
    { id: 8, name: "Elif Yıldız", role: "Garson", status: "pasif", phone: "0539 888 9900", tables: [] },
  ],
  reservations: [
    { id: 1, tableId: 4, guestName: "Hasan Bey", guestCount: 2, date: "28.03.2026", time: "21:00", phone: "0532 111 0001", note: "Doğum günü" },
    { id: 2, tableId: 10, guestName: "Aylin Hanım", guestCount: 3, date: "28.03.2026", time: "20:30", phone: "0533 222 0002", note: "" },
  ],
  dailySales: [
    { hour: "12:00", amount: 1200 },
    { hour: "13:00", amount: 2800 },
    { hour: "14:00", amount: 1900 },
    { hour: "15:00", amount: 800 },
    { hour: "16:00", amount: 600 },
    { hour: "17:00", amount: 1100 },
    { hour: "18:00", amount: 2200 },
    { hour: "19:00", amount: 4100 },
    { hour: "20:00", amount: 3800 },
  ],
  users: [
    { id: 1, username: "admin", password: "admin123", role: "Yönetici", name: "Sistem Yöneticisi" },
    { id: 2, username: "garson1", password: "garson123", role: "Garson", name: "Ahmet Yılmaz" },
    { id: 3, username: "kasiyer1", password: "kasiyer123", role: "Kasiyer", name: "Ayşe Arslan" },
  ],
};

const styles = `
  @import url('https://fonts.googleapis.com/css2?family=Playfair+Display:wght@600;700&family=DM+Sans:wght@300;400;500;600&family=JetBrains+Mono:wght@400;500&display=swap');
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  :root {
    --bg: #0f0e0c; --surface: #1a1916; --surface2: #242220; --border: #2e2c28;
    --accent: #d4a853; --accent2: #c17f3b; --green: #4caf7d; --red: #e05252;
    --blue: #5b8fe8; --orange: #e07a30; --text: #f0ece4; --text2: #9b9589; --text3: #5a5650;
  }
  body { font-family: 'DM Sans', sans-serif; background: var(--bg); color: var(--text); overflow: hidden; }
  .login-wrap { min-height: 100vh; display: flex; align-items: center; justify-content: center; background: radial-gradient(ellipse at center, #1a1612 0%, #0f0e0c 70%); }
  .login-box { background: var(--surface); border: 1px solid var(--border); border-radius: 16px; padding: 40px; width: 380px; text-align: center; }
  .login-title { font-family: 'Playfair Display', serif; font-size: 26px; color: var(--accent); margin-bottom: 4px; }
  .login-sub { font-size: 11px; color: var(--text3); letter-spacing: 2px; text-transform: uppercase; margin-bottom: 28px; }
  .login-input { width: 100%; background: var(--bg); border: 1px solid var(--border); border-radius: 8px; padding: 11px 13px; color: var(--text); font-size: 14px; font-family: 'DM Sans', sans-serif; outline: none; transition: border-color 0.2s; margin-bottom: 12px; display: block; }
  .login-input:focus { border-color: var(--accent); }
  .login-label { display: block; text-align: left; font-size: 11px; color: var(--text3); text-transform: uppercase; letter-spacing: 1px; margin-bottom: 5px; }
  .login-btn { width: 100%; background: var(--accent); color: #1a1410; border: none; border-radius: 8px; padding: 13px; font-size: 15px; font-weight: 600; cursor: pointer; font-family: 'DM Sans', sans-serif; transition: background 0.2s; margin-top: 6px; }
  .login-btn:hover { background: var(--accent2); }
  .login-error { background: rgba(224,82,82,0.1); border: 1px solid rgba(224,82,82,0.3); border-radius: 8px; padding: 10px; font-size: 13px; color: var(--red); margin-bottom: 12px; }
  .login-hint { font-size: 11px; color: var(--text3); margin-top: 16px; }
  .login-hint span { color: var(--text2); font-family: 'JetBrains Mono', monospace; }
  .app { display: flex; height: 100vh; overflow: hidden; }
  .sidebar { width: 220px; min-width: 220px; background: var(--surface); border-right: 1px solid var(--border); display: flex; flex-direction: column; }
  .sidebar-logo { padding: 20px 20px 16px; border-bottom: 1px solid var(--border); }
  .logo-title { font-family: 'Playfair Display', serif; font-size: 17px; color: var(--accent); line-height: 1.2; }
  .logo-sub { font-size: 10px; color: var(--text3); letter-spacing: 2px; text-transform: uppercase; margin-top: 2px; }
  .db-badge { display: inline-flex; align-items: center; gap: 5px; background: rgba(91,143,232,0.1); border: 1px solid rgba(91,143,232,0.2); border-radius: 4px; padding: 3px 8px; font-size: 9px; color: var(--blue); font-family: 'JetBrains Mono', monospace; margin-top: 8px; }
  .db-dot { width: 5px; height: 5px; background: var(--blue); border-radius: 50%; animation: pulse 2s infinite; }
  @keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.3} }
  .nav { flex: 1; padding: 12px 0; overflow-y: auto; }
  .nav-section { padding: 8px 16px 4px; font-size: 9px; color: var(--text3); letter-spacing: 1.5px; text-transform: uppercase; }
  .nav-item { display: flex; align-items: center; gap: 10px; padding: 9px 16px; cursor: pointer; font-size: 13px; color: var(--text2); border-left: 2px solid transparent; transition: all 0.15s; }
  .nav-item:hover { background: var(--surface2); color: var(--text); }
  .nav-item.active { color: var(--accent); border-left-color: var(--accent); background: rgba(212,168,83,0.07); }
  .nav-icon { width: 18px; text-align: center; }
  .nav-badge { margin-left: auto; background: var(--red); color: white; font-size: 10px; padding: 1px 6px; border-radius: 10px; font-weight: 600; }
  .sidebar-footer { padding: 12px 16px; border-top: 1px solid var(--border); }
  .sidebar-user { display: flex; align-items: center; gap: 8px; margin-bottom: 8px; }
  .user-avatar { width: 28px; height: 28px; border-radius: 50%; background: linear-gradient(135deg, var(--accent), var(--accent2)); display: flex; align-items: center; justify-content: center; font-size: 11px; font-weight: 700; color: #1a1410; flex-shrink: 0; }
  .user-name { font-size: 12px; font-weight: 500; }
  .user-role { font-size: 10px; color: var(--text3); }
  .logout-btn { width: 100%; background: transparent; border: 1px solid var(--border); border-radius: 6px; padding: 6px; font-size: 11px; color: var(--text3); cursor: pointer; font-family: 'DM Sans', sans-serif; transition: all 0.15s; }
  .logout-btn:hover { border-color: var(--red); color: var(--red); }
  .main { flex: 1; display: flex; flex-direction: column; overflow: hidden; }
  .topbar { background: var(--surface); border-bottom: 1px solid var(--border); padding: 12px 24px; display: flex; align-items: center; gap: 16px; }
  .topbar-title { font-family: 'Playfair Display', serif; font-size: 20px; flex: 1; }
  .topbar-time { font-family: 'JetBrains Mono', monospace; font-size: 12px; color: var(--text3); }
  .btn { display: inline-flex; align-items: center; gap: 6px; padding: 7px 14px; border-radius: 6px; font-size: 12px; font-weight: 500; cursor: pointer; border: none; transition: all 0.15s; font-family: 'DM Sans', sans-serif; }
  .btn-primary { background: var(--accent); color: #1a1410; }
  .btn-primary:hover { background: var(--accent2); }
  .btn-outline { background: transparent; color: var(--text2); border: 1px solid var(--border); }
  .btn-outline:hover { border-color: var(--accent); color: var(--accent); }
  .btn-danger { background: rgba(224,82,82,0.12); color: var(--red); border: 1px solid rgba(224,82,82,0.2); }
  .btn-success { background: rgba(76,175,125,0.12); color: var(--green); border: 1px solid rgba(76,175,125,0.2); }
  .btn-blue { background: rgba(91,143,232,0.12); color: var(--blue); border: 1px solid rgba(91,143,232,0.2); }
  .content { flex: 1; overflow-y: auto; padding: 24px; }
  .grid-4 { display: grid; grid-template-columns: repeat(4, 1fr); gap: 16px; margin-bottom: 20px; }
  .grid-3 { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; margin-bottom: 20px; }
  .grid-2 { display: grid; grid-template-columns: repeat(2, 1fr); gap: 16px; margin-bottom: 20px; }
  .stat-card { background: var(--surface); border: 1px solid var(--border); border-radius: 10px; padding: 18px; position: relative; overflow: hidden; }
  .stat-card::before { content: ''; position: absolute; top: 0; left: 0; right: 0; height: 2px; }
  .stat-card.gold::before { background: var(--accent); }
  .stat-card.green::before { background: var(--green); }
  .stat-card.blue::before { background: var(--blue); }
  .stat-card.red::before { background: var(--red); }
  .stat-label { font-size: 11px; color: var(--text3); text-transform: uppercase; letter-spacing: 1px; margin-bottom: 6px; }
  .stat-value { font-family: 'Playfair Display', serif; font-size: 28px; color: var(--text); line-height: 1; }
  .stat-sub { font-size: 11px; color: var(--text3); margin-top: 4px; }
  .stat-icon { position: absolute; top: 16px; right: 16px; font-size: 22px; opacity: 0.2; }
  .tables-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 12px; margin-bottom: 20px; }
  .table-card { background: var(--surface); border: 1px solid var(--border); border-radius: 10px; padding: 16px; cursor: pointer; transition: all 0.2s; }
  .table-card:hover { border-color: var(--accent); transform: translateY(-1px); }
  .table-card.selected { border-color: var(--accent); background: rgba(212,168,83,0.06); }
  .table-status { display: inline-block; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600; text-transform: uppercase; letter-spacing: 0.5px; margin-bottom: 8px; }
  .s-bos { background: rgba(76,175,125,0.12); color: var(--green); }
  .s-dolu { background: rgba(224,82,82,0.12); color: var(--red); }
  .s-rezerve { background: rgba(91,143,232,0.12); color: var(--blue); }
  .table-name { font-family: 'Playfair Display', serif; font-size: 18px; margin-bottom: 4px; }
  .table-info { font-size: 11px; color: var(--text3); }
  .table-waiter { font-size: 11px; color: var(--text2); margin-top: 4px; }
  .panel { background: var(--surface); border: 1px solid var(--border); border-radius: 10px; padding: 18px; margin-bottom: 16px; }
  .panel-title { font-family: 'Playfair Display', serif; font-size: 15px; margin-bottom: 16px; display: flex; align-items: center; }
  .order-card { background: var(--surface2); border: 1px solid var(--border); border-radius: 8px; padding: 14px; margin-bottom: 10px; }
  .order-header { display: flex; align-items: center; gap: 10px; margin-bottom: 10px; flex-wrap: wrap; }
  .order-id { font-family: 'JetBrains Mono', monospace; font-size: 12px; color: var(--accent); font-weight: 600; }
  .order-badge { padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600; text-transform: uppercase; }
  .ob-bekliyor { background: rgba(224,122,48,0.12); color: var(--orange); }
  .ob-hazir { background: rgba(91,143,232,0.12); color: var(--blue); }
  .ob-teslim { background: rgba(76,175,125,0.12); color: var(--green); }
  .ob-odendi { background: rgba(90,86,80,0.3); color: var(--text3); }
  .order-total { font-family: 'Playfair Display', serif; font-size: 16px; color: var(--accent); margin-top: 8px; }
  .order-actions { display: flex; gap: 6px; margin-top: 10px; flex-wrap: wrap; }
  .menu-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; }
  .menu-item { background: var(--surface2); border: 1px solid var(--border); border-radius: 8px; padding: 12px; cursor: pointer; transition: all 0.15s; }
  .menu-item:hover { border-color: var(--accent); }
  .menu-item-name { font-size: 13px; font-weight: 500; margin-bottom: 3px; }
  .menu-item-cat { font-size: 10px; color: var(--text3); margin-bottom: 6px; }
  .menu-item-price { font-family: 'Playfair Display', serif; font-size: 15px; color: var(--accent); }
  .chart-wrap { display: flex; align-items: flex-end; gap: 8px; height: 120px; }
  .chart-bar { flex: 1; border-radius: 4px 4px 0 0; background: linear-gradient(to top, var(--accent2), var(--accent)); transition: height 0.5s; cursor: pointer; min-width: 20px; }
  .chart-bar:hover { background: linear-gradient(to top, var(--accent), #f0c875); }
  .chart-labels { display: flex; gap: 8px; margin-top: 4px; }
  .chart-labels > div { flex: 1; min-width: 20px; text-align: center; font-size: 9px; color: var(--text3); }
  .sql-log { background: #0a0c0f; border: 1px solid rgba(91,143,232,0.2); border-radius: 8px; padding: 12px; max-height: 200px; overflow-y: auto; }
  .sql-entry { margin-bottom: 8px; }
  .sql-time { font-family: 'JetBrains Mono', monospace; font-size: 9px; color: var(--text3); margin-bottom: 2px; }
  .sql-query { font-family: 'JetBrains Mono', monospace; font-size: 10px; color: #7bc8a4; white-space: pre-wrap; line-height: 1.5; }
  .sql-rows { font-family: 'JetBrains Mono', monospace; font-size: 9px; color: var(--blue); margin-top: 2px; }
  .form-row { display: flex; gap: 12px; margin-bottom: 12px; }
  .form-group { flex: 1; }
  .form-label { display: block; font-size: 11px; color: var(--text3); margin-bottom: 4px; text-transform: uppercase; letter-spacing: 0.5px; }
  .form-input, .form-select, .form-textarea { width: 100%; background: var(--bg); border: 1px solid var(--border); border-radius: 6px; padding: 8px 10px; color: var(--text); font-size: 13px; font-family: 'DM Sans', sans-serif; outline: none; transition: border-color 0.15s; }
  .form-input:focus, .form-select:focus, .form-textarea:focus { border-color: var(--accent); }
  .form-select option { background: var(--surface); }
  .form-textarea { resize: vertical; min-height: 60px; }
  .staff-card { background: var(--surface2); border: 1px solid var(--border); border-radius: 8px; padding: 14px; display: flex; align-items: center; gap: 12px; margin-bottom: 10px; }
  .staff-avatar { width: 38px; height: 38px; border-radius: 50%; background: linear-gradient(135deg, var(--accent), var(--accent2)); display: flex; align-items: center; justify-content: center; font-weight: 700; font-size: 14px; color: #1a1410; flex-shrink: 0; }
  .badge-active { background: rgba(76,175,125,0.12); color: var(--green); padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600; cursor: pointer; }
  .badge-pasif { background: rgba(90,86,80,0.3); color: var(--text3); padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600; cursor: pointer; }
  .tabs { display: flex; gap: 2px; background: var(--surface2); border-radius: 8px; padding: 3px; margin-bottom: 16px; flex-wrap: wrap; }
  .tab { flex: 1; padding: 7px 10px; border-radius: 6px; cursor: pointer; font-size: 12px; text-align: center; color: var(--text2); transition: all 0.15s; white-space: nowrap; }
  .tab.active { background: var(--surface); color: var(--accent); font-weight: 500; }
  .modal-overlay { position: fixed; inset: 0; background: rgba(0,0,0,0.75); backdrop-filter: blur(4px); display: flex; align-items: center; justify-content: center; z-index: 1000; }
  .modal { background: var(--surface); border: 1px solid var(--border); border-radius: 12px; padding: 24px; width: 520px; max-width: 95vw; max-height: 85vh; overflow-y: auto; }
  .modal-title { font-family: 'Playfair Display', serif; font-size: 18px; color: var(--accent); margin-bottom: 20px; }
  .toast { position: fixed; bottom: 24px; right: 24px; background: var(--surface); border: 1px solid var(--accent); border-radius: 8px; padding: 12px 18px; font-size: 13px; color: var(--text); z-index: 2000; animation: slideUp 0.3s ease; }
  @keyframes slideUp { from { transform: translateY(20px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }
  .res-card { background: var(--surface2); border: 1px solid var(--border); border-radius: 8px; padding: 14px; margin-bottom: 10px; display: flex; align-items: center; gap: 12px; }
  .divider { border: none; border-top: 1px solid var(--border); margin: 12px 0; }
  ::-webkit-scrollbar { width: 4px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb { background: var(--border); border-radius: 2px; }
`;

function Toast({ msg, onClose }) {
  useEffect(() => { const t = setTimeout(onClose, 3000); return () => clearTimeout(t); }, []);
  return <div className="toast">✓ {msg}</div>;
}

function SQLLog({ logs }) {
  return (
    <div className="sql-log">
      {logs.slice(0, 8).map((l, i) => (
        <div key={i} className="sql-entry">
          <div className="sql-time">{l.time}</div>
          <div className="sql-query">{l.query}</div>
          <div className="sql-rows">→ {l.rows} satır döndü</div>
          {i < logs.length - 1 && <hr style={{ border: "none", borderTop: "1px solid #1e2028", margin: "6px 0" }} />}
        </div>
      ))}
      {logs.length === 0 && <div style={{ color: "var(--text3)", fontSize: 11 }}>Henüz sorgu çalıştırılmadı...</div>}
    </div>
  );
}

function Login({ onLogin }) {
  const [username, setUsername] = useState("");
  const [password, setPassword] = useState("");
  const [error, setError] = useState("");
  function handleLogin() {
    const user = DB_INITIAL.users.find(u => u.username === username && u.password === password);
    if (user) { onLogin(user); } else { setError("Kullanıcı adı veya şifre hatalı!"); }
  }
  return (
    <div className="login-wrap">
      <div className="login-box">
        <div className="login-title">🍽️ Restoran</div>
        <div className="login-title" style={{ fontSize: 18 }}>Otomasyon Sistemi</div>
        <div className="login-sub">Yönetim Paneli v1.0</div>
        {error && <div className="login-error">⚠️ {error}</div>}
        <label className="login-label">Kullanıcı Adı</label>
        <input className="login-input" value={username} onChange={e => { setUsername(e.target.value); setError(""); }} onKeyDown={e => e.key === "Enter" && handleLogin()} placeholder="kullanıcı adı" />
        <label className="login-label">Şifre</label>
        <input className="login-input" type="password" value={password} onChange={e => { setPassword(e.target.value); setError(""); }} onKeyDown={e => e.key === "Enter" && handleLogin()} placeholder="••••••••" />
        <button className="login-btn" onClick={handleLogin}>Giriş Yap →</button>
        <div className="login-hint">Test: <span>admin</span> / <span>admin123</span></div>
      </div>
    </div>
  );
}

function Dashboard({ tables, orders, staff, sqlLogs, dailySales }) {
  const maxSales = Math.max(...dailySales.map(d => d.amount));
  const totalToday = dailySales.reduce((s, d) => s + d.amount, 0);
  const busyTables = tables.filter(t => t.status === "dolu").length;
  const pendingOrders = orders.filter(o => o.status === "bekliyor" || o.status === "hazırlanıyor").length;
  return (
    <div>
      <div className="grid-4">
        <div className="stat-card gold"><div className="stat-label">Günlük Ciro</div><div className="stat-value">₺{totalToday.toLocaleString("tr")}</div><div className="stat-sub">Bugün toplam</div><div className="stat-icon">💰</div></div>
        <div className="stat-card red"><div className="stat-label">Dolu Masa</div><div className="stat-value">{busyTables}/{tables.length}</div><div className="stat-sub">%{Math.round(busyTables/tables.length*100)} doluluk</div><div className="stat-icon">🪑</div></div>
        <div className="stat-card blue"><div className="stat-label">Aktif Sipariş</div><div className="stat-value">{pendingOrders}</div><div className="stat-sub">İşlem bekliyor</div><div className="stat-icon">📋</div></div>
        <div className="stat-card green"><div className="stat-label">Aktif Personel</div><div className="stat-value">{staff.filter(s => s.status==="aktif").length}</div><div className="stat-sub">Görevde</div><div className="stat-icon">👥</div></div>
      </div>
      <div className="grid-2">
        <div className="panel">
          <div className="panel-title">📊 Saatlik Satış</div>
          <div className="chart-wrap">
            {dailySales.map((d, i) => <div key={i} className="chart-bar" style={{ height: `${(d.amount/maxSales)*100}%` }} title={`${d.hour}: ₺${d.amount.toLocaleString("tr")}`} />)}
          </div>
          <div className="chart-labels">{dailySales.map((d, i) => <div key={i}>{d.hour.slice(0,5)}</div>)}</div>
        </div>
        <div className="panel">
          <div className="panel-title">🖥️ SQL Server Sorgu Logu</div>
          <SQLLog logs={sqlLogs} />
        </div>
      </div>
      <div className="panel">
        <div className="panel-title">⚡ Aktif Siparişler</div>
        <div style={{ display: "grid", gridTemplateColumns: "1fr 1fr", gap: 10 }}>
          {orders.filter(o => o.status !== "ödendi").map(o => {
            const badge = o.status==="bekliyor"?"ob-bekliyor":o.status==="hazırlanıyor"?"ob-hazir":o.status==="teslim edildi"?"ob-teslim":"ob-odendi";
            return (
              <div className="order-card" key={o.id}>
                <div className="order-header">
                  <span className="order-id">#{o.id}</span>
                  <span className={`order-badge ${badge}`}>{o.status}</span>
                  <span style={{ marginLeft:"auto", fontSize:11, color:"var(--text3)" }}>Masa {o.tableId} · {o.time}</span>
                </div>
                <div style={{ fontSize:12, color:"var(--text2)", lineHeight:1.8 }}>
                  {o.items.map((item,i) => <div key={i}>{item.qty}x {item.name}</div>)}
                </div>
                <div className="order-total">₺{o.total.toLocaleString("tr")}</div>
              </div>
            );
          })}
        </div>
      </div>
    </div>
  );
}

function Tables({ tables, setTables, orders, setOrders, menu, addSQL, showToast }) {
  const [selected, setSelected] = useState(null);
  const [showModal, setShowModal] = useState(false);
  const [cart, setCart] = useState([]);
  const [filterCat, setFilterCat] = useState("Tümü");
  const cats = ["Tümü", ...new Set(menu.map(m => m.category))];
  const tableOrder = selected ? orders.find(o => o.tableId === selected.id && o.status !== "ödendi") : null;
  const statusClass = { "boş":"s-bos","dolu":"s-dolu","rezerve":"s-rezerve" };

  function openTable(t) {
    setSelected(t);
    addSQL(`SELECT o.*, oi.*\nFROM Orders o\nJOIN OrderItems oi ON o.Id = oi.OrderId\nWHERE o.TableId = ${t.id} AND o.Status != 'ödendi'`, 1);
  }
  function addToCart(item) {
    setCart(prev => { const ex = prev.find(c=>c.menuId===item.id); if(ex) return prev.map(c=>c.menuId===item.id?{...c,qty:c.qty+1}:c); return [...prev,{menuId:item.id,name:item.name,qty:1,price:item.price}]; });
  }
  function removeFromCart(menuId) {
    setCart(prev => { const it = prev.find(c=>c.menuId===menuId); if(it&&it.qty>1) return prev.map(c=>c.menuId===menuId?{...c,qty:c.qty-1}:c); return prev.filter(c=>c.menuId!==menuId); });
  }
  function placeOrder() {
    if(!cart.length) return;
    const total = cart.reduce((s,c)=>s+c.qty*c.price,0);
    const newOrder = { id: Date.now()%100000+1000, tableId:selected.id, items:cart, status:"bekliyor", total, time:new Date().toLocaleTimeString("tr-TR",{hour:"2-digit",minute:"2-digit"}), waiter:selected.waiter||"—" };
    setOrders(prev=>[...prev,newOrder]);
    setTables(prev=>prev.map(t=>t.id===selected.id?{...t,status:"dolu"}:t));
    addSQL(`INSERT INTO Orders (TableId,Status,Total,Time)\nVALUES (${selected.id},'bekliyor',${total},'${newOrder.time}')`,1);
    setCart([]); setShowModal(false);
    showToast(`Sipariş #${newOrder.id} oluşturuldu`);
    setSelected(null);
  }
  function changeStatus(orderId, newStatus) {
    setOrders(prev=>prev.map(o=>o.id===orderId?{...o,status:newStatus}:o));
    addSQL(`UPDATE Orders\nSET Status = '${newStatus}'\nWHERE Id = ${orderId}`,1);
    showToast(`Sipariş durumu: ${newStatus}`);
  }
  function closeTable() {
    if(!tableOrder) return;
    setOrders(prev=>prev.map(o=>o.id===tableOrder.id?{...o,status:"ödendi"}:o));
    setTables(prev=>prev.map(t=>t.id===selected.id?{...t,status:"boş",waiter:null}:t));
    addSQL(`UPDATE Orders SET Status='ödendi' WHERE Id=${tableOrder.id};\nUPDATE Tables SET Status='boş',WaiterId=NULL WHERE Id=${selected.id}`,2);
    showToast(`${selected.name} hesabı kapatıldı — ₺${tableOrder.total.toLocaleString("tr")}`);
    setSelected(null);
  }
  const filteredMenu = filterCat==="Tümü"?menu:menu.filter(m=>m.category===filterCat);
  return (
    <div>
      <div className="tables-grid">
        {tables.map(t=>(
          <div key={t.id} className={`table-card ${selected?.id===t.id?"selected":""}`} onClick={()=>openTable(t)}>
            <div className={`table-status ${statusClass[t.status]||"s-bos"}`}>{t.status}</div>
            <div className="table-name">{t.name}</div>
            <div className="table-info">👥 {t.capacity} kişilik</div>
            {t.waiter && <div className="table-waiter">🧑‍🍳 {t.waiter}</div>}
          </div>
        ))}
      </div>
      {selected && (
        <div className="panel">
          <div className="panel-title">
            🪑 {selected.name} — Detay
            <div style={{ marginLeft:"auto", display:"flex", gap:8 }}>
              {selected.status!=="dolu" && <button className="btn btn-primary" onClick={()=>{setCart([]);setShowModal(true);}}>+ Sipariş Al</button>}
              {tableOrder && tableOrder.status==="teslim edildi" && <button className="btn btn-success" onClick={closeTable}>💳 Hesap Kapat</button>}
            </div>
          </div>
          {tableOrder ? (
            <div className="order-card">
              <div className="order-header">
                <span className="order-id">#{tableOrder.id}</span>
                <span className={`order-badge ${tableOrder.status==="bekliyor"?"ob-bekliyor":tableOrder.status==="hazırlanıyor"?"ob-hazir":"ob-teslim"}`}>{tableOrder.status}</span>
                <span style={{ fontSize:11, color:"var(--text3)", marginLeft:"auto" }}>Garson: {tableOrder.waiter}</span>
              </div>
              {tableOrder.items.map((item,i)=>(
                <div key={i} style={{ display:"flex", fontSize:13, padding:"4px 0", borderBottom:"1px solid var(--border)" }}>
                  <span style={{ flex:1 }}>{item.name}</span>
                  <span style={{ color:"var(--text3)" }}>{item.qty}x ₺{item.price}</span>
                  <span style={{ marginLeft:16, color:"var(--accent)", minWidth:70, textAlign:"right" }}>₺{(item.qty*item.price).toLocaleString("tr")}</span>
                </div>
              ))}
              <div className="order-total" style={{ marginTop:10 }}>Toplam: ₺{tableOrder.total.toLocaleString("tr")}</div>
              <div className="order-actions">
                {tableOrder.status==="bekliyor" && <button className="btn btn-blue" onClick={()=>changeStatus(tableOrder.id,"hazırlanıyor")}>👨‍🍳 Hazırlanıyor</button>}
                {tableOrder.status==="hazırlanıyor" && <button className="btn btn-success" onClick={()=>changeStatus(tableOrder.id,"teslim edildi")}>✅ Teslim Edildi</button>}
                {tableOrder.status==="teslim edildi" && <button className="btn btn-success" onClick={closeTable}>💳 Hesabı Kapat</button>}
              </div>
            </div>
          ) : (
            <div style={{ color:"var(--text3)", fontSize:13, textAlign:"center", padding:20 }}>Bu masada aktif sipariş yok</div>
          )}
        </div>
      )}
      {showModal && (
        <div className="modal-overlay" onClick={()=>setShowModal(false)}>
          <div className="modal" onClick={e=>e.stopPropagation()}>
            <div className="modal-title">+ Sipariş Oluştur — {selected?.name}</div>
            <div className="tabs">{cats.map(c=><div key={c} className={`tab ${filterCat===c?"active":""}`} onClick={()=>setFilterCat(c)}>{c}</div>)}</div>
            <div className="menu-grid" style={{ marginBottom:16 }}>
              {filteredMenu.map(item=>(
                <div key={item.id} className="menu-item" onClick={()=>addToCart(item)}>
                  <div className="menu-item-name">{item.name}</div>
                  <div className="menu-item-cat">{item.category}</div>
                  <div className="menu-item-price">₺{item.price}</div>
                </div>
              ))}
            </div>
            {cart.length>0 && (
              <div style={{ background:"var(--surface2)", borderRadius:8, padding:12, marginBottom:14 }}>
                <div style={{ fontSize:11, color:"var(--text3)", marginBottom:8, textTransform:"uppercase", letterSpacing:1 }}>Sepet</div>
                {cart.map((c,i)=>(
                  <div key={i} style={{ display:"flex", fontSize:12, marginBottom:4, alignItems:"center" }}>
                    <span style={{ flex:1 }}>{c.name}</span>
                    <button onClick={()=>removeFromCart(c.menuId)} style={{ background:"none",border:"none",color:"var(--red)",cursor:"pointer",fontSize:16,padding:"0 4px" }}>−</button>
                    <span style={{ color:"var(--text3)",margin:"0 6px" }}>{c.qty}x</span>
                    <button onClick={()=>addToCart({id:c.menuId,name:c.name,price:c.price})} style={{ background:"none",border:"none",color:"var(--green)",cursor:"pointer",fontSize:16,padding:"0 4px" }}>+</button>
                    <span style={{ color:"var(--accent)",marginLeft:8,minWidth:60,textAlign:"right" }}>₺{(c.qty*c.price).toLocaleString("tr")}</span>
                  </div>
                ))}
                <hr className="divider"/>
                <div style={{ display:"flex", fontWeight:600, fontSize:14 }}>
                  <span style={{ flex:1 }}>Toplam</span>
                  <span style={{ color:"var(--accent)" }}>₺{cart.reduce((s,c)=>s+c.qty*c.price,0).toLocaleString("tr")}</span>
                </div>
              </div>
            )}
            <div style={{ display:"flex", gap:8 }}>
              <button className="btn btn-outline" style={{ flex:1 }} onClick={()=>setShowModal(false)}>İptal</button>
              <button className="btn btn-primary" style={{ flex:1 }} onClick={placeOrder} disabled={!cart.length}>✓ Siparişi Onayla</button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
}

function OrdersPage({ orders, setOrders, addSQL, showToast }) {
  const [filter, setFilter] = useState("Tümü");
  const statuses = ["Tümü","bekliyor","hazırlanıyor","teslim edildi","ödendi"];
  function changeStatus(orderId, newStatus) {
    setOrders(prev=>prev.map(o=>o.id===orderId?{...o,status:newStatus}:o));
    addSQL(`UPDATE Orders SET Status='${newStatus}' WHERE Id=${orderId}`,1);
    showToast(`Sipariş #${orderId} → ${newStatus}`);
  }
  const filtered = filter==="Tümü"?orders:orders.filter(o=>o.status===filter);
  return (
    <div>
      <div className="tabs">{statuses.map(s=><div key={s} className={`tab ${filter===s?"active":""}`} onClick={()=>setFilter(s)}>{s}{s!=="Tümü"&&` (${orders.filter(o=>o.status===s).length})`}</div>)}</div>
      {filtered.length===0 && <div style={{ textAlign:"center",color:"var(--text3)",padding:40 }}>Bu kategoride sipariş yok</div>}
      {filtered.map(o=>{
        const badge=o.status==="bekliyor"?"ob-bekliyor":o.status==="hazırlanıyor"?"ob-hazir":o.status==="teslim edildi"?"ob-teslim":"ob-odendi";
        return (
          <div key={o.id} className="order-card">
            <div className="order-header">
              <span className="order-id">#{o.id}</span>
              <span style={{ fontSize:12, color:"var(--text3)" }}>Masa {o.tableId}</span>
              <span className={`order-badge ${badge}`}>{o.status}</span>
              <span style={{ marginLeft:"auto", fontSize:11, color:"var(--text3)" }}>{o.time} · {o.waiter}</span>
            </div>
            <div style={{ fontSize:12, color:"var(--text2)", lineHeight:1.8 }}>
              {o.items.map((item,i)=><span key={i}>{item.qty}x {item.name}{i<o.items.length-1?", ":""}</span>)}
            </div>
            <div className="order-total">₺{o.total.toLocaleString("tr")}</div>
            <div className="order-actions">
              {o.status==="bekliyor" && <button className="btn btn-blue" onClick={()=>changeStatus(o.id,"hazırlanıyor")}>👨‍🍳 Hazırlanıyor</button>}
              {o.status==="hazırlanıyor" && <button className="btn btn-success" onClick={()=>changeStatus(o.id,"teslim edildi")}>✅ Teslim Edildi</button>}
            </div>
          </div>
        );
      })}
    </div>
  );
}

function MenuPage({ menu, setMenu, addSQL, showToast }) {
  const [filterCat, setFilterCat] = useState("Tümü");
  const [showAdd, setShowAdd] = useState(false);
  const [form, setForm] = useState({ name:"", category:"Ana Yemek", price:"", stock:"" });
  const cats = [...new Set(menu.map(m=>m.category))];
  function addItem() {
    if(!form.name||!form.price) return;
    const newItem = { id:Date.now(), ...form, price:+form.price, stock:+form.stock||0 };
    setMenu(prev=>[...prev,newItem]);
    addSQL(`INSERT INTO MenuItems (Name,Category,Price,Stock)\nVALUES ('${form.name}','${form.category}',${form.price},${form.stock||0})`,1);
    showToast(`${form.name} menüye eklendi`);
    setForm({ name:"", category:"Ana Yemek", price:"", stock:"" });
    setShowAdd(false);
  }
  function deleteItem(id,name) {
    setMenu(prev=>prev.filter(m=>m.id!==id));
    addSQL(`DELETE FROM MenuItems WHERE Id=${id}`,1);
    showToast(`${name} silindi`);
  }
  const filtered = filterCat==="Tümü"?menu:menu.filter(m=>m.category===filterCat);
  return (
    <div>
      <div style={{ display:"flex", gap:8, marginBottom:16, flexWrap:"wrap" }}>
        {["Tümü",...cats].map(c=>(
          <div key={c} className={`tab ${filterCat===c?"active":""}`} style={{ background:filterCat===c?"var(--surface)":"var(--surface2)", padding:"7px 14px", borderRadius:6, cursor:"pointer" }} onClick={()=>setFilterCat(c)}>{c}</div>
        ))}
        <button className="btn btn-primary" style={{ marginLeft:"auto" }} onClick={()=>setShowAdd(true)}>+ Ürün Ekle</button>
      </div>
      <div className="menu-grid">
        {filtered.map(item=>(
          <div key={item.id} className="menu-item" style={{ position:"relative" }}>
            <div className="menu-item-name">{item.name}</div>
            <div className="menu-item-cat">{item.category}</div>
            <div style={{ display:"flex", alignItems:"center", marginTop:6 }}>
              <span className="menu-item-price">₺{item.price}</span>
              <span style={{ marginLeft:"auto", fontSize:10, color:"var(--text3)" }}>Stok: {item.stock}</span>
              <button className="btn btn-danger" style={{ marginLeft:8, padding:"3px 8px", fontSize:10 }} onClick={e=>{e.stopPropagation();deleteItem(item.id,item.name);}}>✕</button>
            </div>
          </div>
        ))}
      </div>
      {showAdd && (
        <div className="modal-overlay" onClick={()=>setShowAdd(false)}>
          <div className="modal" onClick={e=>e.stopPropagation()}>
            <div className="modal-title">+ Yeni Ürün Ekle</div>
            <div className="form-row"><div className="form-group"><label className="form-label">Ürün Adı</label><input className="form-input" value={form.name} onChange={e=>setForm(p=>({...p,name:e.target.value}))} placeholder="Ürün adı girin"/></div></div>
            <div className="form-row">
              <div className="form-group"><label className="form-label">Kategori</label><select className="form-select" value={form.category} onChange={e=>setForm(p=>({...p,category:e.target.value}))}>{cats.map(c=><option key={c}>{c}</option>)}</select></div>
              <div className="form-group"><label className="form-label">Fiyat (₺)</label><input className="form-input" type="number" value={form.price} onChange={e=>setForm(p=>({...p,price:e.target.value}))} placeholder="0"/></div>
              <div className="form-group"><label className="form-label">Stok</label><input className="form-input" type="number" value={form.stock} onChange={e=>setForm(p=>({...p,stock:e.target.value}))} placeholder="0"/></div>
            </div>
            <div style={{ display:"flex", gap:8 }}>
              <button className="btn btn-outline" style={{ flex:1 }} onClick={()=>setShowAdd(false)}>İptal</button>
              <button className="btn btn-primary" style={{ flex:1 }} onClick={addItem}>Ekle</button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
}

function StaffPage({ staff, setStaff, addSQL, showToast }) {
  const [showAdd, setShowAdd] = useState(false);
  const [form, setForm] = useState({ name:"", role:"Garson", phone:"" });
  function addStaff() {
    if(!form.name) return;
    const newStaff = { id:Date.now(), ...form, status:"aktif", tables:[] };
    setStaff(prev=>[...prev,newStaff]);
    addSQL(`INSERT INTO Staff (Name,Role,Phone,Status)\nVALUES ('${form.name}','${form.role}','${form.phone}','aktif')`,1);
    showToast(`${form.name} personele eklendi`);
    setForm({ name:"", role:"Garson", phone:"" });
    setShowAdd(false);
  }
  function removeStaff(id,name) {
    setStaff(prev=>prev.filter(s=>s.id!==id));
    addSQL(`DELETE FROM Staff WHERE Id=${id}`,1);
    showToast(`${name} çıkarıldı`);
  }
  function toggleStatus(id) { setStaff(prev=>prev.map(s=>s.id===id?{...s,status:s.status==="aktif"?"pasif":"aktif"}:s)); }
  return (
    <div>
      <div style={{ display:"flex", justifyContent:"flex-end", marginBottom:16 }}>
        <button className="btn btn-primary" onClick={()=>setShowAdd(true)}>+ Personel Ekle</button>
      </div>
      <div style={{ display:"grid", gridTemplateColumns:"1fr 1fr", gap:10 }}>
        {staff.map(s=>(
          <div key={s.id} className="staff-card">
            <div className="staff-avatar">{s.name.charAt(0)}</div>
            <div style={{ flex:1 }}>
              <div style={{ fontSize:13, fontWeight:500 }}>{s.name}</div>
              <div style={{ fontSize:11, color:"var(--text3)" }}>{s.role}</div>
              {s.phone && <div style={{ fontSize:11, color:"var(--text2)", fontFamily:"JetBrains Mono,monospace" }}>{s.phone}</div>}
            </div>
            <div style={{ display:"flex", flexDirection:"column", gap:6, alignItems:"flex-end" }}>
              <span className={s.status==="aktif"?"badge-active":"badge-pasif"} onClick={()=>toggleStatus(s.id)}>{s.status}</span>
              <button className="btn btn-danger" style={{ padding:"2px 8px", fontSize:10 }} onClick={()=>removeStaff(s.id,s.name)}>✕ Sil</button>
            </div>
          </div>
        ))}
      </div>
      {showAdd && (
        <div className="modal-overlay" onClick={()=>setShowAdd(false)}>
          <div className="modal" onClick={e=>e.stopPropagation()}>
            <div className="modal-title">+ Yeni Personel Ekle</div>
            <div className="form-group" style={{ marginBottom:12 }}><label className="form-label">Ad Soyad</label><input className="form-input" value={form.name} onChange={e=>setForm(p=>({...p,name:e.target.value}))} placeholder="Personel adı"/></div>
            <div className="form-row">
              <div className="form-group"><label className="form-label">Görev</label><select className="form-select" value={form.role} onChange={e=>setForm(p=>({...p,role:e.target.value}))}><option>Garson</option><option>Aşçı</option><option>Kasiyer</option><option>Yönetici</option></select></div>
              <div className="form-group"><label className="form-label">Telefon</label><input className="form-input" value={form.phone} onChange={e=>setForm(p=>({...p,phone:e.target.value}))} placeholder="0532 xxx xxxx"/></div>
            </div>
            <div style={{ display:"flex", gap:8 }}>
              <button className="btn btn-outline" style={{ flex:1 }} onClick={()=>setShowAdd(false)}>İptal</button>
              <button className="btn btn-primary" style={{ flex:1 }} onClick={addStaff}>Ekle</button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
}

function ReservationsPage({ reservations, setReservations, tables, addSQL, showToast }) {
  const [showAdd, setShowAdd] = useState(false);
  const [form, setForm] = useState({ tableId:4, guestName:"", guestCount:2, date:"", time:"", phone:"", note:"" });
  function addReservation() {
    if(!form.guestName||!form.date||!form.time) return;
    const newRes = { id:Date.now(), ...form, tableId:+form.tableId, guestCount:+form.guestCount };
    setReservations(prev=>[...prev,newRes]);
    addSQL(`INSERT INTO Reservations (TableId,GuestName,GuestCount,Date,Time,Phone)\nVALUES (${form.tableId},'${form.guestName}',${form.guestCount},'${form.date}','${form.time}','${form.phone}')`,1);
    showToast(`${form.guestName} için rezervasyon oluşturuldu`);
    setForm({ tableId:4, guestName:"", guestCount:2, date:"", time:"", phone:"", note:"" });
    setShowAdd(false);
  }
  function deleteRes(id,name) {
    setReservations(prev=>prev.filter(r=>r.id!==id));
    addSQL(`DELETE FROM Reservations WHERE Id=${id}`,1);
    showToast(`${name} rezervasyonu iptal edildi`);
  }
  return (
    <div>
      <div style={{ display:"flex", justifyContent:"flex-end", marginBottom:16 }}>
        <button className="btn btn-primary" onClick={()=>setShowAdd(true)}>+ Rezervasyon Ekle</button>
      </div>
      {reservations.length===0 && <div style={{ textAlign:"center",color:"var(--text3)",padding:40 }}>Aktif rezervasyon bulunmuyor</div>}
      {reservations.map(r=>(
        <div key={r.id} className="res-card">
          <span style={{ fontSize:24 }}>📅</span>
          <div style={{ flex:1 }}>
            <div style={{ fontSize:14, fontWeight:500 }}>{r.guestName}</div>
            <div style={{ fontSize:11, color:"var(--text3)", marginTop:2 }}>{r.date} · {r.time} · {r.guestCount} kişi · Masa {r.tableId}{r.phone&&` · ${r.phone}`}</div>
            {r.note && <div style={{ fontSize:11, color:"var(--accent)", marginTop:2 }}>📝 {r.note}</div>}
          </div>
          <button className="btn btn-danger" onClick={()=>deleteRes(r.id,r.guestName)}>İptal</button>
        </div>
      ))}
      {showAdd && (
        <div className="modal-overlay" onClick={()=>setShowAdd(false)}>
          <div className="modal" onClick={e=>e.stopPropagation()}>
            <div className="modal-title">📅 Yeni Rezervasyon</div>
            <div className="form-row">
              <div className="form-group"><label className="form-label">Misafir Adı</label><input className="form-input" value={form.guestName} onChange={e=>setForm(p=>({...p,guestName:e.target.value}))} placeholder="Ad Soyad"/></div>
              <div className="form-group"><label className="form-label">Kişi Sayısı</label><input className="form-input" type="number" value={form.guestCount} onChange={e=>setForm(p=>({...p,guestCount:e.target.value}))}/></div>
            </div>
            <div className="form-row">
              <div className="form-group"><label className="form-label">Masa</label><select className="form-select" value={form.tableId} onChange={e=>setForm(p=>({...p,tableId:e.target.value}))}>{tables.filter(t=>t.status!=="dolu").map(t=><option key={t.id} value={t.id}>{t.name} ({t.capacity} kişilik)</option>)}</select></div>
              <div className="form-group"><label className="form-label">Telefon</label><input className="form-input" value={form.phone} onChange={e=>setForm(p=>({...p,phone:e.target.value}))} placeholder="0532 xxx xxxx"/></div>
            </div>
            <div className="form-row">
              <div className="form-group"><label className="form-label">Tarih</label><input className="form-input" value={form.date} onChange={e=>setForm(p=>({...p,date:e.target.value}))} placeholder="GG.AA.YYYY"/></div>
              <div className="form-group"><label className="form-label">Saat</label><input className="form-input" value={form.time} onChange={e=>setForm(p=>({...p,time:e.target.value}))} placeholder="SS:DD"/></div>
            </div>
            <div className="form-group" style={{ marginBottom:14 }}><label className="form-label">Not</label><textarea className="form-textarea" value={form.note} onChange={e=>setForm(p=>({...p,note:e.target.value}))} placeholder="Özel istek, doğum günü vb."/></div>
            <div style={{ display:"flex", gap:8 }}>
              <button className="btn btn-outline" style={{ flex:1 }} onClick={()=>setShowAdd(false)}>İptal</button>
              <button className="btn btn-primary" style={{ flex:1 }} onClick={addReservation}>Kaydet</button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
}

function ReportsPage({ orders, addSQL }) {
  useEffect(()=>{
    addSQL(`SELECT SUM(Total) AS GunlukCiro, COUNT(*) AS SiparisAdedi\nFROM Orders WHERE CAST(CreatedAt AS DATE)=CAST(GETDATE() AS DATE)`,1);
  },[]);
  const topItems = {};
  orders.forEach(o=>o.items.forEach(i=>{ topItems[i.name]=(topItems[i.name]||0)+i.qty; }));
  const topList = Object.entries(topItems).sort((a,b)=>b[1]-a[1]).slice(0,10);
  const totalRevenue = orders.reduce((s,o)=>s+o.total,0);
  const avgOrder = orders.length?Math.round(totalRevenue/orders.length):0;
  const paidOrders = orders.filter(o=>o.status==="ödendi").length;
  return (
    <div>
      <div className="grid-3">
        <div className="stat-card gold"><div className="stat-label">Toplam Ciro</div><div className="stat-value">₺{totalRevenue.toLocaleString("tr")}</div><div className="stat-sub">KDV dahil</div><div className="stat-icon">💰</div></div>
        <div className="stat-card blue"><div className="stat-label">Toplam Sipariş</div><div className="stat-value">{orders.length}</div><div className="stat-sub">{paidOrders} tamamlandı</div><div className="stat-icon">📋</div></div>
        <div className="stat-card green"><div className="stat-label">Ortalama Sipariş</div><div className="stat-value">₺{avgOrder.toLocaleString("tr")}</div><div className="stat-sub">Sipariş başı</div><div className="stat-icon">📊</div></div>
      </div>
      <div className="panel">
        <div className="panel-title">🏆 En Çok Sipariş Edilen Ürünler</div>
        <div style={{ fontFamily:"JetBrains Mono,monospace", fontSize:10, color:"var(--text3)", marginBottom:12, background:"var(--surface2)", padding:"8px 12px", borderRadius:6 }}>
          SELECT mi.Name, SUM(oi.Qty) AS ToplamAdet FROM OrderItems oi JOIN MenuItems mi ON oi.MenuItemId=mi.Id GROUP BY mi.Name ORDER BY ToplamAdet DESC
        </div>
        {topList.map(([name,qty],i)=>{
          const maxQty=topList[0][1];
          return (
            <div key={name} style={{ display:"flex", alignItems:"center", gap:10, marginBottom:10 }}>
              <span style={{ width:20, color:"var(--text3)", fontSize:11 }}>#{i+1}</span>
              <span style={{ flex:1, fontSize:13 }}>{name}</span>
              <div style={{ width:120, background:"var(--surface2)", borderRadius:3, height:6, overflow:"hidden" }}>
                <div style={{ width:`${(qty/maxQty)*100}%`, height:"100%", background:"var(--accent)" }}/>
              </div>
              <span style={{ color:"var(--accent)", fontSize:12, fontFamily:"JetBrains Mono,monospace", minWidth:24 }}>{qty}</span>
            </div>
          );
        })}
      </div>
    </div>
  );
}

export default function App() {
  const [loggedIn, setLoggedIn] = useState(false);
  const [currentUser, setCurrentUser] = useState(null);
  const [page, setPage] = useState("dashboard");
  const [tables, setTables] = useState(DB_INITIAL.tables);
  const [orders, setOrders] = useState(DB_INITIAL.orders);
  const [menu, setMenu] = useState(DB_INITIAL.menu);
  const [staff, setStaff] = useState(DB_INITIAL.staff);
  const [reservations, setReservations] = useState(DB_INITIAL.reservations);
  const [dailySales] = useState(DB_INITIAL.dailySales);
  const [sqlLogs, setSqlLogs] = useState([]);
  const [toast, setToast] = useState(null);
  const [time, setTime] = useState(new Date().toLocaleTimeString("tr-TR"));

  useEffect(()=>{ const t=setInterval(()=>setTime(new Date().toLocaleTimeString("tr-TR")),1000); return ()=>clearInterval(t); },[]);

  function addSQL(query, rows=0) {
    const entry = { time:new Date().toLocaleTimeString("tr-TR"), query, rows:rows||Math.floor(Math.random()*10)+1 };
    setSqlLogs(prev=>[entry,...prev].slice(0,20));
  }
  function showToast(msg) { setToast(msg); setTimeout(()=>setToast(null),3000); }
  function handleLogin(user) {
    setCurrentUser(user); setLoggedIn(true);
    addSQL(`SELECT * FROM Users WHERE Username='${user.username}' AND IsActive=1`,1);
    addSQL("SELECT COUNT(*) AS MasaSayisi FROM Tables;\nSELECT COUNT(*) AS MenuItemSayisi FROM MenuItems",2);
  }
  function handleLogout() { setLoggedIn(false); setCurrentUser(null); setPage("dashboard"); }

  if(!loggedIn) return (<><style>{styles}</style><Login onLogin={handleLogin}/></>);

  const pendingCount = orders.filter(o=>o.status==="bekliyor").length;
  const nav = [
    { key:"dashboard", icon:"⊞", label:"Dashboard" },
    { key:"tables", icon:"🪑", label:"Masalar" },
    { key:"orders", icon:"📋", label:"Siparişler", badge:pendingCount||null },
    { key:"menu", icon:"🍽️", label:"Menü" },
    { key:"staff", icon:"👥", label:"Personel" },
    { key:"reservations", icon:"📅", label:"Rezervasyon" },
    { key:"reports", icon:"📊", label:"Raporlar" },
  ];
  const titles = { dashboard:"Dashboard", tables:"Masa Yönetimi", orders:"Sipariş Takibi", menu:"Menü Yönetimi", staff:"Personel Yönetimi", reservations:"Rezervasyon", reports:"Raporlar" };

  return (
    <>
      <style>{styles}</style>
      <div className="app">
        <div className="sidebar">
          <div className="sidebar-logo">
            <div className="logo-title">🍽️ Restoran<br/>Otomasyon</div>
            <div className="logo-sub">Yönetim Sistemi</div>
            <div className="db-badge"><div className="db-dot"/>SQL Server 2022</div>
          </div>
          <nav className="nav">
            <div className="nav-section">Ana Modüller</div>
            {nav.map(n=>(
              <div key={n.key} className={`nav-item ${page===n.key?"active":""}`} onClick={()=>setPage(n.key)}>
                <span className="nav-icon">{n.icon}</span>
                {n.label}
                {n.badge && <span className="nav-badge">{n.badge}</span>}
              </div>
            ))}
          </nav>
          <div className="sidebar-footer">
            <div className="sidebar-user">
              <div className="user-avatar">{currentUser?.name?.charAt(0)}</div>
              <div>
                <div className="user-name">{currentUser?.name}</div>
                <div className="user-role">{currentUser?.role}</div>
              </div>
            </div>
            <button className="logout-btn" onClick={handleLogout}>⏻ Çıkış Yap</button>
            <div style={{ fontSize:9, color:"var(--text3)", marginTop:6 }}>RestoDB · localhost:1433</div>
          </div>
        </div>
        <div className="main">
          <div className="topbar">
            <div className="topbar-title">{titles[page]}</div>
            <div className="topbar-time">🕐 {time}</div>
          </div>
          <div className="content">
            {page==="dashboard" && <Dashboard tables={tables} orders={orders} staff={staff} sqlLogs={sqlLogs} dailySales={dailySales}/>}
            {page==="tables" && <Tables tables={tables} setTables={setTables} orders={orders} setOrders={setOrders} menu={menu} addSQL={addSQL} showToast={showToast}/>}
            {page==="orders" && <OrdersPage orders={orders} setOrders={setOrders} addSQL={addSQL} showToast={showToast}/>}
            {page==="menu" && <MenuPage menu={menu} setMenu={setMenu} addSQL={addSQL} showToast={showToast}/>}
            {page==="staff" && <StaffPage staff={staff} setStaff={setStaff} addSQL={addSQL} showToast={showToast}/>}
            {page==="reservations" && <ReservationsPage reservations={reservations} setReservations={setReservations} tables={tables} addSQL={addSQL} showToast={showToast}/>}
            {page==="reports" && <ReportsPage orders={orders} addSQL={addSQL}/>}
          </div>
        </div>
      </div>
      {toast && <Toast msg={toast} onClose={()=>setToast(null)}/>}
    </>
  );
}
