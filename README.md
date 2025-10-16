<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistem Pemeriksaan Batching Plant</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.2/css/all.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.23/jspdf.plugin.autotable.min.js"></script>
    <style>
        body { 
            font-family: 'Inter', sans-serif; 
            background-color: #f8f9fa; 
        }
        #main-app-container { 
            display: flex; 
            flex-direction: column; 
            min-height: 100vh; 
        }
        main { 
            flex-grow: 1; 
        }
        .step-indicator { 
            transition: all 0.3s ease; 
        }
        .form-section { 
            display: none; 
        }
        .form-section.active { 
            display: block; 
        }
        .custom-radio input[type="radio"] { 
            display: none; 
        }
        .custom-radio label { 
            cursor: pointer; 
            padding: 0.5rem 1rem; 
            border-radius: 9999px; 
            border: 1px solid #D1D5DB; 
            transition: all 0.2s ease; 
            font-size: 0.875rem; 
        }
        .custom-radio input[type="radio"]:checked + label { 
            background-color: #3B82F6; 
            color: white; 
            border-color: #3B82F6; 
        }
        .custom-radio label:hover { 
            background-color: #F3F4F6; 
        }
        .custom-radio input[type="radio"]:checked + label:hover { 
            background-color: #2563EB; 
        }
        .alert-message { 
            display: none; 
            margin-top: 1rem; 
            padding: 0.75rem 1.25rem; 
            border-radius: 0.5rem; 
            color: #721c24; 
            background-color: #f8d7da; 
            border: 1px solid #f5c6cb; 
        }
        .rekap-radio-cell { 
            text-align: center; 
        }
        .rekap-radio-cell input[type="radio"] { 
            -webkit-appearance: none; 
            appearance: none; 
            background-color: #fff; 
            margin: 0; 
            font: inherit; 
            color: #9ca3af; 
            width: 1.15em; 
            height: 1.15em; 
            border: 0.15em solid currentColor; 
            border-radius: 50%; 
            transform: translateY(-0.075em); 
            display: inline-grid; 
            place-content: center; 
        }
        .rekap-radio-cell input[type="radio"]::before { 
            content: ""; 
            width: 0.65em; 
            height: 0.65em; 
            border-radius: 50%; 
            transform: scale(0); 
            transition: 120ms transform ease-in-out; 
            box-shadow: inset 1em 1em #3B82F6; 
        }
        .rekap-radio-cell input[type="radio"]:checked { 
            border-color: #3B82F6; 
        }
        .rekap-radio-cell input[type="radio"]:checked::before { 
            transform: scale(1); 
        }
        .rekap-radio-cell input[type="radio"]:disabled { 
            cursor: not-allowed; 
            border-color: #d1d5db; 
        }
        .chevron-icon { 
            transition: transform 0.3s ease; 
        }
        .chevron-icon.rotate-180 { 
            transform: rotate(180deg); 
        }
        .collapsible-content { 
            max-height: 0; 
            overflow: hidden; 
            transition: max-height 0.5s ease-out; 
        }
        .collapsible-content.open { 
            max-height: 5000px; 
        }
        #dashboard-page, #form-page { 
            display: none; 
        }
        #dashboard-page.active, #form-page.active { 
            display: block; 
        }
        .modal-overlay { 
            display: none; 
            position: fixed; 
            z-index: 50; 
            top: 0; 
            left: 0; 
            right: 0; 
            bottom: 0; 
            background-color: rgba(0, 0, 0, 0.5); 
            align-items: center; 
            justify-content: center; 
        }
        .modal-overlay.open { 
            display: flex; 
        }
        .validation-error { 
            background-color: #fee2e2 !important; 
            border-color: #fca5a5 !important; 
        }
        @media print {
            body > *:not(#print-area) { 
                display: none !important; 
            }
            #print-area { 
                display: block !important; 
                margin: 2rem; 
            }
            #print-area h2 { 
                font-size: 1.5rem; 
                font-weight: bold; 
                margin-bottom: 1rem; 
                text-align: center; 
            }
            #print-area table { 
                width: 100%; 
                border-collapse: collapse; 
            }
            #print-area th, #print-area td { 
                border: 1px solid #ccc; 
                padding: 8px; 
                font-size: 10px; 
            }
        }
    </style>
</head>
<body>
<div id="main-app-container">
    <header class="bg-gray-800 text-white shadow-md sticky top-0 z-40 no-print">
        <nav class="container mx-auto px-6 py-4 flex justify-between items-center">
            <div class="flex items-center space-x-2">
                <i class="fas fa-industry text-yellow-400 text-2xl"></i>
                <a href="#" class="text-xl font-bold">Sistem Pemeriksaan Batching Plant</a>
            </div>
            <div class="hidden md:flex items-center space-x-6">
                <a href="#" class="hover:text-yellow-400" id="nav-dashboard">Dashboard</a>
                <a href="#" class="hover:text-yellow-400" id="nav-registration">Pendaftaran</a>
                <a href="#" class="hover:text-yellow-400">Pemeriksaan</a>
                <a href="#" class="hover:text-yellow-400">Sertifikat</a>
                <a href="#" class="hover:text-yellow-400">Laporan</a>
            </div>
            <div class="flex items-center space-x-4">
                 <a href="#" class="hidden md:flex items-center space-x-2 hover:text-yellow-400">
                    <i class="fas fa-users"></i>
                    <span>Tim Pemeriksa</span>
                </a>
                <a href="#" class="bg-red-600 hover:bg-red-700 px-4 py-2 rounded-md font-semibold transition">Logout</a>
            </div>
        </nav>
    </header>
    <main>
        <!-- Dashboard Page -->
        <div id="dashboard-page" class="active">
             <div class="container mx-auto p-4 sm:p-6 lg:p-8">
                <h1 class="text-2xl font-bold text-gray-800 mb-6">Dashboard Pemeriksaan Batching Plant</h1>
                <!-- Stats Cards -->
                <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
                    <div class="bg-white p-6 rounded-lg shadow-sm flex items-start justify-between">
                        <div>
                            <p class="text-sm text-gray-500">Total Pemeriksaan</p>
                            <p class="text-3xl font-bold text-gray-800">--</p>
                            <p class="text-xs text-gray-400 mt-1">Pemeriksaan bulan ini</p>
                        </div>
                        <div class="bg-blue-100 text-blue-600 p-3 rounded-full">
                            <i class="fas fa-clipboard-list fa-lg"></i>
                        </div>
                    </div>
                    <div class="bg-white p-6 rounded-lg shadow-sm flex items-start justify-between">
                        <div>
                            <p class="text-sm text-gray-500">Laik Operasi</p>
                            <p class="text-3xl font-bold text-gray-800">--</p>
                            <p class="text-xs text-gray-400 mt-1">Sertifikat diterbitkan</p>
                        </div>
                        <div class="bg-green-100 text-green-600 p-3 rounded-full">
                            <i class="fas fa-check-circle fa-lg"></i>
                        </div>
                    </div>
                    <div class="bg-white p-6 rounded-lg shadow-sm flex items-start justify-between">
                        <div>
                            <p class="text-sm text-gray-500">Perlu Perbaikan</p>
                            <p class="text-3xl font-bold text-gray-800">--</p>
                            <p class="text-xs text-gray-400 mt-1">Menunggu perbaikan</p>
                        </div>
                        <div class="bg-yellow-100 text-yellow-600 p-3 rounded-full">
                            <i class="fas fa-tools fa-lg"></i>
                        </div>
                    </div>
                    <div class="bg-white p-6 rounded-lg shadow-sm flex items-start justify-between">
                        <div>
                            <p class="text-sm text-gray-500">Tidak Laik</p>
                            <p class="text-3xl font-bold text-gray-800">--</p>
                            <p class="text-xs text-gray-400 mt-1">Tidak memenuhi syarat</p>
                        </div>
                        <div class="bg-red-100 text-red-600 p-3 rounded-full">
                            <i class="fas fa-times-circle fa-lg"></i>
                        </div>
                    </div>
                </div>
                <!-- Action Buttons -->
                <div class="flex items-center space-x-4 mb-8">
                     <button id="open-registration-modal-btn" class="bg-green-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-green-700 transition duration-300 flex items-center">
                        <i class="fas fa-plus-circle mr-2"></i> Registrasi Batching Plant Baru
                    </button>
                    <button id="start-new-inspection-btn" class="bg-blue-600 text-white font-semibold py-2 px-4 rounded-lg hover:bg-blue-700 transition duration-300 flex items-center">
                        <i class="fas fa-file-alt mr-2"></i> Mulai Pemeriksaan Baru
                    </button>
                </div>
                <!-- Recent Inspections Table -->
                <div class="bg-white p-6 rounded-lg shadow-sm">
                    <h2 class="text-xl font-bold text-gray-800 mb-4">Pemeriksaan Terbaru</h2>
                    <div class="overflow-x-auto">
                        <table class="min-w-full bg-white">
                            <thead class="bg-gray-100">
                                <tr>
                                    <th class="py-3 px-4 text-left text-xs font-semibold text-gray-600 uppercase">ID Pemeriksaan</th>
                                    <th class="py-3 px-4 text-left text-xs font-semibold text-gray-600 uppercase">Nama Perusahaan</th>
                                    <th class="py-3 px-4 text-left text-xs font-semibold text-gray-600 uppercase">Lokasi</th>
                                    <th class="py-3 px-4 text-left text-xs font-semibold text-gray-600 uppercase">Tanggal</th>
                                    <th class="py-3 px-4 text-left text-xs font-semibold text-gray-600 uppercase">Status</th>
                                    <th class="py-3 px-4 text-left text-xs font-semibold text-gray-600 uppercase">Aksi</th>
                                </tr>
                            </thead>
                            <tbody class="text-gray-700">
                                <tr>
                                    <td class="py-3 px-4 border-b">BP-2025-001</td>
                                    <td class="py-3 px-4 border-b">PT.--</td>
                                    <td class="py-3 px-4 border-b">--</td>
                                    <td class="py-3 px-4 border-b">--</td>
                                    <td class="py-3 px-4 border-b">
                                        <span class="bg-green-100 text-green-700 text-xs font-medium px-2 py-1 rounded-full">Laik Operasi</span>
                                    </td>
                                    <td class="py-3 px-4 border-b">
                                        <button class="bg-blue-500 text-white text-xs font-semibold py-1 px-3 rounded-md hover:bg-blue-600">Detail</button>
                                    </td>
                                </tr>
                                <tr>
                                    <td class="py-3 px-4 border-b">BP-2025-002</td>
                                    <td class="py-3 px-4 border-b">CV.--</td>
                                    <td class="py-3 px-4 border-b">--</td>
                                    <td class="py-3 px-4 border-b">--</td>
                                    <td class="py-3 px-4 border-b">
                                        <span class="bg-yellow-100 text-yellow-700 text-xs font-medium px-2 py-1 rounded-full">Perbaikan</span>
                                    </td>
                                    <td class="py-3 px-4 border-b">
                                        <button class="bg-blue-500 text-white text-xs font-semibold py-1 px-3 rounded-md hover:bg-blue-600">Detail</button>
                                    </td>
                                </tr>
                                <tr>
                                    <td class="py-3 px-4 border-b">BP-2025-003</td>
                                    <td class="py-3 px-4 border-b">PT.--</td>
                                    <td class="py-3 px-4 border-b">--</td>
                                    <td class="py-3 px-4 border-b">--</td>
                                    <td class="py-3 px-4 border-b">
                                        <span class="bg-green-100 text-green-700 text-xs font-medium px-2 py-1 rounded-full">Laik Operasi</span>
                                    </td>
                                    <td class="py-3 px-4 border-b">
                                        <button class="bg-blue-500 text-white text-xs font-semibold py-1 px-3 rounded-md hover:bg-blue-600">Detail</button>
                                    </td>
                                </tr>
                                <tr>
                                    <td class="py-3 px-4">BP-2025-004</td>
                                    <td class="py-3 px-4">PT.--</td>
                                    <td class="py-3 px-4">--</td>
                                    <td class="py-3 px-4">--</td>
                                    <td class="py-3 px-4">
                                        <span class="bg-red-100 text-red-700 text-xs font-medium px-2 py-1 rounded-full">Tidak Laik</span>
                                    </td>
                                    <td class="py-3 px-4">
                                        <button class="bg-blue-500 text-white text-xs font-semibold py-1 px-3 rounded-md hover:bg-blue-600">Detail</button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
        <!-- Form Page -->
        <div id="form-page">
           <!-- Form content will be injected here by script -->
        </div>
    </main>
    <footer class="bg-gray-800 text-white text-center py-4 mt-auto no-print">
        <p class="text-sm">&copy; 2025 Sistem Pemeriksaan Batching Plant - Direktorat Jenderal Bina Marga</p>
        <p class="text-xs text-gray-400">Kementerian Pekerjaan Umum dan Perumahan Rakyat</p>
        <p class="text-xs text-gray-400">Aktualisasi Andy Adiansyah & Nindya Fitria Sari</p>
    </footer>
</div>
<!-- Registration Modal -->
<div id="registration-modal" class="modal-overlay no-print">
    <div class="bg-white rounded-lg shadow-xl w-full max-w-3xl max-h-[90vh] flex flex-col">
        <div class="p-6 border-b flex justify-between items-center">
            <h2 class="text-xl font-bold text-gray-800">Formulir Pendaftaran Batching Plant</h2>
            <button id="close-modal-btn" class="text-gray-500 hover:text-gray-800">&times;</button>
        </div>
        <div class="p-6 overflow-y-auto">
            <form id="registration-form" class="space-y-6">
                <div>
                    <label for="reg-company-name" class="block text-sm font-medium text-gray-700 mb-1">Nama Perusahaan</label>
                    <input type="text" id="reg-company-name" class="w-full p-2 border border-gray-300 rounded-md shadow-sm" required>
                </div>
                <div>
                    <label for="reg-location" class="block text-sm font-medium text-gray-700 mb-1">Lokasi Batching Plant</label>
                    <textarea id="reg-location" class="w-full p-2 border border-gray-300 rounded-md shadow-sm" rows="2" required></textarea>
                </div>
                <div>
                    <label for="reg-gps" class="block text-sm font-medium text-gray-700 mb-1">Koordinat GPS (Latitude, Longitude)</label>
                    <div class="flex items-center gap-2">
                        <input type="text" id="reg-gps" class="w-full p-2 border border-gray-300 rounded-md shadow-sm" placeholder="-6.200000, 106.816666" required>
                        <button type="button" id="get-gps-btn" class="bg-blue-100 text-blue-700 p-2 rounded-md hover:bg-blue-200">
                            <i class="fas fa-map-marker-alt"></i>
                        </button>
                    </div>
                </div>
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-1">Foto Dokumentasi Batching Plant</label>
                    <div class="mt-1 flex justify-center px-6 pt-5 pb-6 border-2 border-gray-300 border-dashed rounded-md">
                        <div class="space-y-1 text-center">
                            <svg class="mx-auto h-12 w-12 text-gray-400" stroke="currentColor" fill="none" viewBox="0 0 48 48" aria-hidden="true">
                                <path d="M28 8H12a4 4 0 00-4 4v20m32-12v8m0 0v8a4 4 0 01-4 4H12a4 4 0 01-4-4v-4m32-4l-3.172-3.172a4 4 0 00-5.656 0L28 28M8 32l9.172-9.172a4 4 0 015.656 0L28 28m0 0l4 4m4-24h8m-4-4v8" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"></path>
                            </svg>
                            <div class="flex text-sm text-gray-600">
                                <label for="reg-photos" class="relative cursor-pointer bg-white rounded-md font-medium text-indigo-600 hover:text-indigo-500 focus-within:outline-none"><span>Unggah file</span>
                                    <input id="reg-photos" name="reg-photos" type="file" class="sr-only" multiple accept="image/*">
                                </label>
                                <p class="pl-1">atau tarik dan lepas</p>
                            </div>
                            <p class="text-xs text-gray-500">PNG, JPG, GIF hingga 2MB per foto (Maks 10 foto)</p>
                        </div>
                    </div>
                    <div id="photo-preview-container" class="mt-4 grid grid-cols-2 sm:grid-cols-3 md:grid-cols-5 gap-4"></div>
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <div>
                        <label for="reg-submission-date" class="block text-sm font-medium text-gray-700 mb-1">Tanggal Pengajuan</label>
                        <input type="date" id="reg-submission-date" class="w-full p-2 border border-gray-300 rounded-md shadow-sm" required>
                    </div>
                    <div>
                        <label for="reg-brand" class="block text-sm font-medium text-gray-700 mb-1">Tipe/Merek Batching Plant</label>
                        <input type="text" id="reg-brand" class="w-full p-2 border border-gray-300 rounded-md shadow-sm" required>
                    </div>
                    <div>
                        <label for="reg-year" class="block text-sm font-medium text-gray-700 mb-1">Tahun Pembuatan</label>
                        <input type="number" id="reg-year" class="w-full p-2 border border-gray-300 rounded-md shadow-sm" required>
                    </div>
                    <div>
                        <label for="reg-capacity" class="block text-sm font-medium text-gray-700 mb-1">Kapasitas (m³/jam)</label>
                        <input type="number" id="reg-capacity" class="w-full p-2 border border-gray-300 rounded-md shadow-sm" required>
                    </div>
                </div>
                <div>
                    <label for="reg-dmf" class="block text-sm font-medium text-gray-700 mb-1">Upload PDF Desain Mix Formula</label>
                    <input type="file" id="reg-dmf" class="w-full text-sm text-gray-500 file:mr-4 file:py-2 file:px-4 file:rounded-full file:border-0 file:text-sm file:font-semibold file:bg-blue-50 file:text-blue-700 hover:file:bg-blue-100" accept=".pdf" required>
                </div>
            </form>
        </div>
        <div class="p-6 bg-gray-50 border-t flex justify-end">
            <button type="button" id="cancel-registration-btn" class="bg-gray-200 text-gray-700 font-bold py-2 px-4 rounded-lg hover:bg-gray-300 mr-2">Batal</button>
            <button type="submit" form="registration-form" class="bg-green-600 text-white font-bold py-2 px-4 rounded-lg hover:bg-green-700">Daftarkan</button>
        </div>
    </div>
</div>
<div id="print-area" class="hidden"></div>
    <script>
        document.addEventListener('DOMContentLoaded', function () {
            // --- DATA STRUCTURES ---
            // Data untuk Tahap I (sudah diubah sesuai permintaan)
            const tahap1Data = { 
                A:{title:"Penyimpanan Material",items:[
                    {id:"A1",title:"AGREGAT",items:[
                        "Dinding/Sekat Stockyard",
                        "Lantai Stockyard",
                        "Atap Penutup Stockyard",
                        "Dinding Bin Penampung",
                        "Pintu Pengeluaran",
                        "Atap Penutup Bin Penampung",
                        "Roll Pemutar Collector",
                        "Motor Penggetar Bin Penampung",
                        "Rantai/V-Belt Pemutar Collector",
                        "Roll Penyangga Belt Conveyor Pemasok",
                        "Belt Conveyor Pemasok ke Timbangan",
                        "Konstruksi Penyangga Conveyor Pemasok",
                        "Atap Penutup Conveyor Pemasok",
                        "Roll Conveyor Pemasok",
                        "Motor Pemutar Conveyor",
                        "Sekop Pengangkat**)"
                    ]},
                    {id:"A2",title:"SEMEN",items:[
                        "Silo",
                        "Pipa Penyalur Pengisian",
                        "Silinder Pengisian",
                        "Kompresor Pengisian",
                        "Pipa Penyalur Penimbang",
                        "Motor Penggerak Penyalur",
                        "Indikator Volume Silo"
                    ]},
                    {id:"A3",title:"BAHAN TAMBAH MINERAL",items:[
                        "Silo",
                        "Pipa Penyalur Pengisian",
                        "Silinder Pengisian",
                        "Kompresor Pengisian",
                        "Pipa Penyalur Penimbangan",
                        "Motor Penggerak Penyalur",
                        "Indikator Volume Silo"
                    ]},
                    {id:"A4",title:"AIR",items:[
                        "Tangki Air",
                        "Atap Penutup Tempat Air",
                        "Kran Penyalur Pengisian",
                        "Pipa Penyalur",
                        "Pipa Pengisian",
                        "Indikator/Meter Volume Isi"
                    ]},
                    {id:"A5",title:"ADMIXTURE",items:[
                        "Penyimpanan/Bin",
                        "Atap Penutup Tempat Admixture",
                        "Pipa Penyalur",
                        "Indikator/Meter Volume Isi"
                    ]}
                ]},
                B:{title:"Penimbangan",items:[
                    {id:"B1",title:"AGREGAT",items:[
                        "Skala Penimbangan",
                        "Pengatur Otomatis Timbangan",
                        "Pintu-Pintu Bukaan",
                        "Silinder-Silinder Pembuka Pintu"
                    ]},
                    {id:"B2",title:"SEMEN",items:[
                        "Skala Timbangan",
                        "Pintu Bukaan/Kran",
                        "Pengatur Otomatis Timbangan"
                    ]},
                    {id:"B3",title:"BAHAN TAMBAH MINERAL",items:[
                        "Skala Timbangan",
                        "Pintu Bukaan/Kran",
                        "Pengatur Otomatis Timbangan"
                    ]},
                    {id:"B4",title:"AIR",items:[
                        "Skala Penimbangan",
                        "Pintu/Kran Pengeluaran",
                        "Pengatur Otomatis Timbangan"
                    ]},
                    {id:"B5",title:"ADMIXTURE",items:[
                        "Skala Penimbangan",
                        "Kran Pengeluaran",
                        "Pengatur Otomatis Timbangan"
                    ]}
                ]},
                C:{title:"Pencampuran/Mixer",items:[
                    {id:"C1",title:"PAN MIXER ***)",items:[
                        "Pan",
                        "Lengan Pengaduk/Paddle",
                        "Paddle Tips",
                        "Poros Pengaduk",
                        "Motor Pemutar",
                        "Pintu Pengeluaran",
                        "Silinder Pembuka Pintu",
                        "Pipa/Selang",
                        "Kompresor",
                        "Corong Penyalur",
                        "Roda Gigi/Gear Pemutar"
                    ]},
                    {id:"C2",title:"PUGMILL ***)",items:[
                        "Poros Pengaduk",
                        "Lengan-Lengan Pengaduk",
                        "Piringan Pengaduk/Paddle Tip",
                        "Silinder/Bucket/Pugmill",
                        "Roda Gigi Pemutar",
                        "Motor Pemutar",
                        "Rantai/V-Belt",
                        "Pintu Pembuangan",
                        "Silinder (Tuas) Pintu Pembuangan"
                    ]},
                    {id:"C3",title:"FREE FALL MIXER ***)",items:[
                        "Silinder atau Drum",
                        "Sudu-Sudu Keong",
                        "Motor Pemutar Hidrolik",
                        "Poros/Bantalan Atas",
                        "Mekanisme Pemutar Drum",
                        "Roll Penyangga Drum",
                        "Tangki Air",
                        "Tangki Hidrolik",
                        "Pompa Hidrolik",
                        "Corong Pengisian",
                        "Corong Pengeluaran",
                        "Truck Unit"
                    ]},
                    {id:"C4",title:"REVERSIBLE MIXER ***)",items:[
                        "Drum",
                        "Skip Loader/Hopper",
                        "Sling Pengangkat",
                        "Roda",
                        "Mesin Pemutar",
                        "Roda Gigi",
                        "Konstruksi Mixer"
                    ]},
                    {id:"C5",title:"PENCAMPUR ANGKAT ***)",items:[
                        "Drum",
                        "Roda Gigi Pemutar",
                        "Roda Pemutar",
                        "Mesin Pemutar",
                        "Dudukan Kereta",
                        "Roda"
                    ]}
                ]},
                D:{title:"Transport/Truck Mixer",items:[
                    {id:"D1",title:"TRUCK",items:[
                        "Mesin",
                        "Sistem Rem",
                        "Lampu dan Elektrikal"
                    ]},
                    {id:"D2",title:"DRUM",items:[
                        "Drum",
                        "Sudu-Sudu keong",
                        "Tangki Air",
                        "Motor Pemutar Hidrolik",
                        "Roll Penyangga",
                        "Poros Putar Atas",
                        "Mekanisme Pemutar",
                        "Tangki Hidrolik",
                        "Pompa Hidrolik",
                        "Corong Pengeluaran",
                        "Corong pengisian"
                    ]}
                ]},
                E:{title:"Laboratorium Pengujian",items:[
                    {id:"E1",title:"Laboratorium Pengujian",isSingle:!0,items:[
                        "Alat Uji Kuat Tekan",
                        "Alat Uji Kuat Lentur",
                        "Alat Uji Slump",
                        "Alat Uji Setting Time",
                        "Alat Uji Kadar Air",
                        "Alat Uji Berat Jenis",
                        "Alat Uji Bobot Isi dan Rongga Udara dalam Agregat",
                        "Saringan/Ayakan",
                        "Alat Uji Berat Isi Beton Segar",
                        "Alat Capping Benda Uji Silinder Beton",
                        "Bak Perendaman"
                    ]}
                ]}
            };
            // Data untuk Tahap II (tetap sama)
            const tahap2Data = { 
                A:{title:"Penyimpanan Material",items:[
                    {id:"A1",title:"AGREGAT",items:[
                        "Pintu Pengeluaran",
                        "Penggetar Bin",
                        "Belt Conveyor Collector",
                        "Roll Pemutar Collector",
                        "Motor Pemutar Collector",
                        "Rantai/V-belt Pemutar Collector",
                        "Roll Penyangga Collector",
                        "Belt Conveyor Pemasok",
                        "Roll Pemutar Conveyor Pemasok",
                        "Motor Pemutar Pemasok",
                        "Rantai/V-Belt Pemutar Conveyor Pemasok",
                        "Roll Penyangga Conveyor Pemasok"
                    ]},
                    {id:"A2",title:"SEMEN",items:[
                        "Ulir Silinder Ke Penimbangan",
                        "Motor Pemutar Ulir Pengeluaran",
                        "Kompresor Untuk Pengisian Silo",
                        "Indikator Volume Silo"
                    ]},
                    {id:"A3",title:"BAHAN TAMBAH MINERAL",items:[
                        "Uji Silinder Ke Penimbangan",
                        "Motor Pemutar Ulir Pengeluaran",
                        "Kompresor Untuk Pengisian Silo",
                        "Indikator Volume Silo"
                    ]},
                    {id:"A4",title:"AIR",items:[
                        "Kran Penyalur Pengisian",
                        "Kran Pengeluaran Air",
                        "Indikator Volume Tangki Air",
                        "Pompa Air"
                    ]},
                    {id:"A5",title:"ADMIXTURE",items:[
                        "Pengatur Pengeluaran",
                        "Dozing Pump"
                    ]}
                ]},
                B:{title:"Penimbangan",items:[
                    {id:"B1",title:"AGREGAT",items:[
                        "Skala Penimbangan",
                        "Pengatur Otomatis Timbangan",
                        "Pintu-Pintu Bukaan",
                        "Silinder-Silinder Pembuka Pintu"
                    ]},
                    {id:"B2",title:"SEMEN",items:[
                        "Skala Timbangan",
                        "Pintu Bukaan/Kran",
                        "Pengatur Otomatis Timbangan"
                    ]},
                    {id:"B3",title:"BAHAN TAMBAH MINERAL",items:[
                        "Skala Timbangan",
                        "Pintu Bukaan/Kran",
                        "Pengatur Otomatis Timbangan"
                    ]},
                    {id:"B4",title:"AIR",items:[
                        "Skala Penimbangan",
                        "Pintu/Kran Pengeluaran",
                        "Pengatur Otomatis Timbangan"
                    ]},
                    {id:"B5",title:"ADMIXTURE",items:[
                        "Skala Penimbangan",
                        "Kran Pengeluaran",
                        "Pengatur Otomatis Timbangan"
                    ]}
                ]},
                C:{title:"Pencampuran/Mixer",items:[
                    {id:"C1",title:"PAN MIXER ***)",items:[
                        "Pan",
                        "Lengan Pengaduk/Paddle",
                        "Paddle Tip",
                        "Poros Pengaduk",
                        "Motor Pemutar",
                        "Pintu Pengeluaran",
                        "Silinder Pembuka Pintu",
                        "Pipa/Selang",
                        "Kompresor",
                        "Corong Penyalur",
                        "Roda Gigi/Gear Pemutar"
                    ]},
                    {id:"C2",title:"PUGMILL ***)",items:[
                        "Poros Pengaduk",
                        "Lengan-Lengan Pengaduk",
                        "Piringan Pengaduk/Paddle Tip",
                        "Silinder/Bucket/Pugmill",
                        "Roda Gigi Pemutar",
                        "Motor Pemutar",
                        "Rantai/V-Belt",
                        "Pintu Pembuangan",
                        "Silinder (Tuas) Pintu Pembuangan"
                    ]},
                    {id:"C3",title:"FREE FALL MIXER ***)",items:[
                        "Silinder atau Drum",
                        "Sudu-Sudu Keong",
                        "Motor Pemutar Hidrolik",
                        "Poros/Bantalan Atas",
                        "Mekanisme Pemutar Drum",
                        "Roll Penyangga Drum",
                        "Tangki Air",
                        "Tangki Hidrolik",
                        "Pompa Hidrolik",
                        "Corong Pengisian",
                        "Corong Pengeluaran",
                        "Truck Unit"
                    ]},
                    {id:"C4",title:"REVERSIBLE MIXER ***)",items:[
                        "Drum",
                        "Skip Loader/Hopper",
                        "Sling Pengangkat",
                        "Roda",
                        "Mesin Pemutar",
                        "Roda Gigi",
                        "Konstruksi Mixer"
                    ]},
                    {id:"C5",title:"PENCAMPUR ANGKAT ***)",items:[
                        "Drum",
                        "Roda Gigi Pemutar",
                        "Roda Pemutar",
                        "Mesin Pemutar",
                        "Dudukan Kereta",
                        "Roda"
                    ]}
                ]},
                D:{title:"Transport/Truck Mixer",items:[
                    {id:"D1",title:"TRUCK",items:[
                        "Mesin",
                        "Sistem Rem",
                        "Lampu/Elektrikal"
                    ]},
                    {id:"D2",title:"DRUM",items:[
                        "Drum",
                        "Sudu-Sudu keong",
                        "Tangki Air",
                        "Motor Pemutar Hidrolik",
                        "Roll Penyangga",
                        "Poros Putar Atas",
                        "Mekanisme Pemutar",
                        "Tangki Hidrolik",
                        "Pompa Hidrolik",
                        "Corong Pengeluaran",
                        "Corong pengisian"
                    ]}
                ]},
                E:{title:"Laboratorium Pengujian",items:[
                    {id:"E1",title:"Laboratorium Pengujian",isSingle:!0,items:[
                        "Alat Uji Kuat Tekan",
                        "Alat Uji Kuat Lentur",
                        "Alat Uji Setting Time",
                        "Alat Uji Kadar Air",
                        "Alat Uji Slump",
                        "Alat Uji Berat Isi Beton Segar",
                        "Alat Capping Benda Uji Silinder Beton"
                    ]}
                ]}
            };
            const tahap3Data = {
                t3_items:[
                    {pengujian:"Berat per meter kubik yang dihitung berdasarkan bebas rongga udara (kg/m3)",max:16,rowId:"t3_1"},
                    {pengujian:"Kadar rongga udara, volume % dari beton",max:1,rowId:"t3_2"},
                    {pengujian:"Slump (mm)",max:25,rowId:"t3_3"},
                    {pengujian:"Kadar Agregat Kasar, berat porsi dari setiap benda uji yang tertahan ayakan No.4 (4,75 mm), %",max:6,rowId:"t3_4"},
                    {pengujian:"Berat isi mortar bebas udara (...) %",max:1.6,rowId:"t3_5"},
                    {pengujian:"Kuat tekan rata-rata pada umur 7 (tujuh) hari (...), %",max:7.5,rowId:"t3_6"},
                    {pengujian:"Temperatur Beton Segar, °C",max:35,rowId:"t3_7"}
                ],
                prod_items:[]
            };
            const formPage = document.getElementById('form-page');
            const dashboardPage = document.getElementById('dashboard-page');
            const startNewInspectionBtn = document.getElementById('start-new-inspection-btn');
            const navDashboard = document.getElementById('nav-dashboard');
            let formInitialized = false;
            // --- ALL HELPER FUNCTIONS DEFINED AT TOP LEVEL ---
            function generateFullFormHTML() {
                const infoUmumHTML = 
                `<div id="step-1" class="form-section active">
                    <h2 class="text-xl font-semibold mb-6 border-b pb-4">Informasi Umum</h2>
                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <div>
                            <label for="owner-name" class="block text-sm font-medium text-gray-700 mb-1">Nama Pemilik</label>
                            <input type="text" id="owner-name" class="w-full p-2 border border-gray-300 rounded-md shadow-sm" required>
                        </div>
                        <div>
                            <label for="location" class="block text-sm font-medium text-gray-700 mb-1">Lokasi</label>
                            <textarea id="location" class="w-full p-2 border border-gray-300 rounded-md shadow-sm resize-y" rows="1" required></textarea>
                        </div>
                        <div>
                            <label for="brand-type" class="block text-sm font-medium text-gray-700 mb-1">Merek/Tipe</label>
                            <input type="text" id="brand-type" class="w-full p-2 border border-gray-300 rounded-md shadow-sm" required>
                        </div>
                        <div>
                            <label for="year" class="block text-sm font-medium text-gray-700 mb-1">Tahun Pembuatan</label>
                            <input type="number" id="year" class="w-full p-2 border border-gray-300 rounded-md shadow-sm" required>
                        </div>
                        <div>
                            <label for="capacity" class="block text-sm font-medium text-gray-700 mb-1">Kapasitas Produksi (m³/jam)</label>
                            <input type="number" id="capacity" class="w-full p-2 border border-gray-300 rounded-md shadow-sm" required>
                        </div>
                        <div>
                            <label for="type" class="block text-sm font-medium text-gray-700 mb-1">Jenis</label>
                            <input type="text" id="type" class="w-full p-2 border border-gray-300 rounded-md shadow-sm" required>
                        </div>
                        <div class="md:col-span-2">
                            <label for="inspection-date" class="block text-sm font-medium text-gray-700 mb-1">Tanggal Pemeriksaan</label>
                            <input type="date" id="inspection-date" class="w-full p-2 border border-gray-300 rounded-md shadow-sm" required>
                        </div>
                    </div>
                </div>`;
                const tahap1HTML = 
                `<div id="step-2" class="form-section">
                    <h2 class="text-xl font-semibold mb-2">Tahap I: Pemeriksaan Teknis</h2>
                    <p class="text-sm text-gray-500 mb-6">Kondisi Peralatan Tidak Dihidupkan</p>
                    <div class="space-y-4" id="tahap1-details"></div>
                    <div class="mt-6 pt-6 border-t">
                        <h3 class="font-semibold text-md mb-3">Kesimpulan Pemeriksaan Tahap I</h3>
                        <div class="flex gap-4 custom-radio" id="conclusion-step-2">
                            <input type="radio" id="s1_fail" name="conclusion_tahap1" value="fail">
                            <label for="s1_fail">Harus Diperbaiki</label>
                            <input type="radio" id="s1_pass" name="conclusion_tahap1" value="pass">
                            <label for="s1_pass">Siap Pemeriksaan Tahap II</label>
                        </div>
                        <div id="alert-step-2" class="alert-message">Pilih "Siap Pemeriksaan Tahap II" untuk melanjutkan.</div>
                    </div>
                </div>`;
                const tahap2HTML = 
                `<div id="step-3" class="form-section">
                    <h2 class="text-xl font-semibold mb-2">Tahap II: Pemeriksaan Teknis</h2>
                    <p class="text-sm text-gray-500 mb-6">Kondisi Peralatan Dihidupkan</p>
                    <div class="space-y-4" id="tahap2-details"></div>
                    <div class="mt-6 pt-6 border-t">
                        <h3 class="font-semibold text-md mb-3">Kesimpulan Pemeriksaan Tahap II</h3>
                        <div class="flex gap-4 custom-radio" id="conclusion-step-3">
                            <input type="radio" id="s2_fail" name="conclusion_tahap2" value="fail">
                            <label for="s2_fail">Harus Diperbaiki</label>
                            <input type="radio" id="s2_pass" name="conclusion_tahap2" value="pass">
                            <label for="s2_pass">Siap Pemeriksaan Tahap III</label>
                        </div>
                        <div id="alert-step-3" class="alert-message">Pilih "Siap Pemeriksaan Tahap III" untuk melanjutkan.</div>
                    </div>
                </div>`;
                const tahap3HTML = 
                `<div id="step-4" class="form-section">
                    <h2 class="text-xl font-semibold mb-2">Tahap III & Kelaikan Produksi</h2>
                    <div class="space-y-6" id="tahap3-details"></div>
                    <div class="mt-6 pt-6 border-t">
                        <h3 class="font-semibold text-md mb-3">Kesimpulan Akhir Pemeriksaan</h3>
                        <div class="flex gap-4 custom-radio" id="conclusion-step-4">
                            <input type="radio" id="s4_fail" name="conclusion_final" value="fail">
                            <label for="s4_fail">Harus Diperbaiki Tahap III</label>
                            <input type="radio" id="s4_pass" name="conclusion_final" value="pass">
                            <label for="s4_pass">Laik Produksi</label>
                        </div>
                    </div>
                </div>`;
                const selesaiHTML = 
                `<div id="step-5" class="form-section text-center">
                    <svg class="mx-auto h-16 w-16 text-green-500" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                    </svg>
                    <h2 class="mt-4 text-2xl font-semibold">Pemeriksaan Selesai</h2>
                    <p class="mt-2 text-gray-600">Semua data telah diisi. Silakan tinjau kembali sebelum mengirimkan.</p>
                    <div class="mt-8">
                        <button type="button" class="w-full sm:w-auto bg-blue-600 text-white font-bold py-3 px-6 rounded-lg hover:bg-blue-700 transition duration-300">Kirim Laporan Pemeriksaan</button>
                    </div>
                </div>`;
                return `<div class="container mx-auto p-4 sm:p-6 lg:p-8 max-w-5xl">
                    <div class="bg-gradient-to-r from-blue-800 to-indigo-900 text-white p-6 rounded-xl shadow-lg mb-8 flex justify-between items-center">
                        <div>
                            <h1 class="text-2xl font-bold">Pemeriksaan Batching Plant</h1>
                            <p class="text-indigo-200">Sertifikasi Laik Operasi dan Laik Produksi</p>
                        </div>
                        <div>
                            <button id="back-to-dashboard-btn" class="bg-gray-600 hover:bg-gray-700 text-white font-semibold py-2 px-4 rounded-lg transition mr-2">
                                <i class="fas fa-arrow-left mr-2"></i> Kembali ke Dashboard
                            </button>
                            <button class="bg-green-500 hover:bg-green-600 text-white font-semibold py-2 px-4 rounded-lg transition">
                                <i class="fas fa-save mr-2"></i> Simpan
                            </button>
                        </div>
                    </div>
                    <div class="mb-8">
                        <div class="flex items-center justify-between mb-2">
                            <div class="step-indicator flex-1 text-center">
                                <div id="step-dot-1" class="mx-auto w-8 h-8 rounded-full border-2 flex items-center justify-center">1</div>
                                <p id="step-text-1" class="text-xs mt-1">Info Umum</p>
                            </div>
                            <div class="flex-1 h-1 bg-gray-300"></div>
                            <div class="step-indicator flex-1 text-center">
                                <div id="step-dot-2" class="mx-auto w-8 h-8 rounded-full border-2 flex items-center justify-center">2</div>
                                <p id="step-text-2" class="text-xs mt-1">Tahap I</p>
                            </div>
                            <div class="flex-1 h-1 bg-gray-300"></div>
                            <div class="step-indicator flex-1 text-center">
                                <div id="step-dot-3" class="mx-auto w-8 h-8 rounded-full border-2 flex items-center justify-center">3</div>
                                <p id="step-text-3" class="text-xs mt-1">Tahap II</p>
                            </div>
                            <div class="flex-1 h-1 bg-gray-300"></div>
                            <div class="step-indicator flex-1 text-center">
                                <div id="step-dot-4" class="mx-auto w-8 h-8 rounded-full border-2 flex items-center justify-center">4</div>
                                <p id="step-text-4" class="text-xs mt-1">Tahap III</p>
                            </div>
                            <div class="flex-1 h-1 bg-gray-300"></div>
                            <div class="step-indicator flex-1 text-center">
                                <div id="step-dot-5" class="mx-auto w-8 h-8 rounded-full border-2 flex items-center justify-center">5</div>
                                <p id="step-text-5" class="text-xs mt-1">Selesai</p>
                            </div>
                        </div>
                    </div>
                    <main id="inspection-form" class="bg-white p-6 sm:p-8 rounded-xl shadow-lg">
                        ${infoUmumHTML}
                        ${tahap1HTML}
                        ${tahap2HTML}
                        ${tahap3HTML}
                        ${selesaiHTML}
                        <div class="mt-8 pt-6 border-t flex justify-between">
                            <button id="prev-btn" type="button" class="bg-gray-200 text-gray-700 font-bold py-2 px-4 rounded-lg hover:bg-gray-300 transition duration-300 disabled:opacity-50 disabled:cursor-not-allowed">Sebelumnya</button>
                            <button id="next-btn" type="button" class="bg-blue-600 text-white font-bold py-2 px-4 rounded-lg hover:bg-blue-700 transition duration-300">Selanjutnya</button>
                        </div>
                    </main>
                </div>`;
            }
            function generateForm(container, data, tahap) {
                container.innerHTML = ''; 
                Object.entries(data).forEach(([mainKey, mainValue]) => {
                    const mainSectionHTML = 
                    `<div class="bg-gray-50 rounded-lg border">
                        <div class="font-semibold cursor-pointer p-4 flex justify-between items-center" data-toggle-parent="t${tahap}-${mainKey}-content">
                            <span>${mainKey}. ${mainValue.title}</span>
                            <svg class="w-5 h-5 transform transition-transform chevron-icon" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
                            </svg>
                        </div>
                        <div class="collapsible-content" id="t${tahap}-${mainKey}-content"></div>
                    </div>`;
                    container.insertAdjacentHTML('beforeend', mainSectionHTML);
                    const mainSectionContent = container.querySelector(`#t${tahap}-${mainKey}-content`);
                    let sectionHTML = '';
                    if (mainKey === 'C') { 
                        sectionHTML += 
                        `<div class="p-4 border-t">
                            <div class="mb-4">
                                <label for="mixer-type-select-t${tahap}" class="block text-sm font-medium text-gray-700 mb-1">Pilih Tipe Mixer</label>
                                <select id="mixer-type-select-t${tahap}" class="w-full max-w-sm p-2 border border-gray-300 rounded-md shadow-sm focus:ring-blue-500 focus:border-blue-500">
                                    <option value="">-- Pilih Salah Satu --</option>`;
                        mainValue.items.forEach(subItem => { 
                            sectionHTML += 
                            `<option value="${subItem.id}">${subItem.title.replace(/\s\*\*\*\)/, '')}</option>`; 
                        });
                        sectionHTML += `</select></div><div class="space-y-2">`;
                        mainValue.items.forEach(subItem => {
                            sectionHTML += `<div id="t${tahap}-mixer-type-${subItem.id}" class="mixer-type-section-t${tahap}" style="display: none;">${generateSubSectionHTML(subItem,`t${tahap}-${subItem.id}-items`,tahap)}</div>`;
                        });
                        sectionHTML += `</div></div>`;
                    } else {
                        let subSectionsHTML = '<div class="p-4 border-t space-y-2">';
                        mainValue.items.forEach(subItem => { subSectionsHTML += generateSubSectionHTML(subItem,`t${tahap}-${subItem.id}-items`,tahap); });
                        subSectionsHTML += '</div>';
                        sectionHTML = subSectionsHTML;
                    }
                    mainSectionContent.innerHTML = sectionHTML;
                });
                const rekapHTML = `<div class="bg-blue-50 rounded-lg border">
                    <div class="font-semibold cursor-pointer p-4 flex justify-between items-center text-blue-800" data-toggle-parent="rekap-t${tahap}-content">
                        <span>Rekapitulasi Pemeriksaan Tahap ${tahap===1?'I':'II'}</span>
                        <svg class="w-5 h-5 transform transition-transform chevron-icon" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
                        </svg>
                    </div>
                    <div class="collapsible-content" id="rekap-t${tahap}-content">
                        <div class="p-4 border-t overflow-x-auto">
                            <table class="min-w-full bg-white border border-gray-300">
                                ${generateRekapTableHead(tahap)}
                                <tbody class="text-sm" id="rekap-t${tahap}-tbody"></tbody>
                            </table>
                             <div class="mt-4 text-right">
                                <button class="print-rekap-btn text-sm bg-red-600 text-white py-2 px-4 rounded-md hover:bg-red-700" data-tahap="${tahap}">
                                    <i class="fas fa-print mr-2"></i>Cetak PDF
                                </button>
                            </div>
                        </div>
                    </div>
                </div>`;
                container.insertAdjacentHTML('beforeend', rekapHTML);
                generateRekapTBody(document.getElementById(`rekap-t${tahap}-tbody`), data, tahap);
            }
            function generateSubSectionHTML(subItem, subContentId, tahap) {
                 const romanNumerals = ['I', 'II', 'III', 'IV', 'V'];
                 const options = tahap === 1 
                    ? `<input type="radio" value="baik" data-rekap-group="rekap-t${tahap}-${subItem.id}" id="s${tahap}-${subItem.id}-{{index}}_b" name="s${tahap}-${subItem.id}-{{index}}">
                    <label for="s${tahap}-${subItem.id}-{{index}}_b">Baik</label>
                    <input type="radio" value="rusak-lkp" data-rekap-group="rekap-t${tahap}-${subItem.id}" id="s${tahap}-${subItem.id}-{{index}}_rl" name="s${tahap}-${subItem.id}-{{index}}">
                    <label for="s${tahap}-${subItem.id}-{{index}}_rl">Rusak (Lkp)</label>
                    <input type="radio" value="rusak-tdk-lkp" data-rekap-group="rekap-t${tahap}-${subItem.id}" id="s${tahap}-${subItem.id}-{{index}}_rtl" name="s${tahap}-${subItem.id}-{{index}}">
                    <label for="s${tahap}-${subItem.id}-{{index}}_rtl">Rusak (Tdk. Lkp)</label>
                    <input type="radio" value="tidak-ada" data-rekap-group="rekap-t${tahap}-${subItem.id}" id="s${tahap}-${subItem.id}-{{index}}_t" name="s${tahap}-${subItem.id}-{{index}}">
                    <label for="s${tahap}-${subItem.id}-{{index}}_t">Tidak Ada</label>`
                    : `<input type="radio" value="baik" data-rekap-group="rekap-t${tahap}-${subItem.id}" id="s${tahap}-${subItem.id}-{{index}}_b" name="s${tahap}-${subItem.id}-{{index}}">
                    <label for="s${tahap}-${subItem.id}-{{index}}_b">Baik/Lancar</label>
                    <input type="radio" value="rusak" data-rekap-group="rekap-t${tahap}-${subItem.id}" id="s${tahap}-${subItem.id}-{{index}}_r" name="s${tahap}-${subItem.id}-{{index}}">
                    <label for="s${tahap}-${subItem.id}-{{index}}_r">Rusak/Tdk Lancar</label>
                    <input type="radio" value="mati" data-rekap-group="rekap-t${tahap}-${subItem.id}" id="s${tahap}-${subItem.id}-{{index}}_m" name="s${tahap}-${subItem.id}-{{index}}">
                    <label for="s${tahap}-${subItem.id}-{{index}}_m">Tidak Hidup</label>`;
                let html = subItem.isSingle ? `<table class="min-w-full divide-y divide-gray-200"><tbody class="bg-white divide-y divide-gray-200">`
                    : `<div class="bg-white rounded border">
                        <div class="font-semibold text-sm cursor-pointer p-3 flex justify-between items-center" data-toggle-group="${subContentId}">
                            <span>${romanNumerals[parseInt(subItem.id.slice(1))-1] || subItem.id.slice(1)}. ${subItem.title}</span>
                            <svg class="w-4 h-4 transform transition-transform chevron-icon" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
                            </svg>
                        </div>
                        <div class="collapsible-content ${subContentId}">
                            <table class="min-w-full divide-y divide-gray-200">
                                <thead class="bg-gray-100">
                                    <tr>
                                        <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Bagian</th>
                                        <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Kondisi</th>
                                        <th class="px-4 py-2 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Keterangan</th>
                                    </tr>
                                </thead>
                                <tbody class="bg-white divide-y divide-gray-200">`;
                subItem.items.forEach((item, index) => {
                     html += `<tr>
                        <td class="px-4 py-3 text-sm">${item}</td>
                        <td class="px-4 py-3">
                            <div class="flex flex-wrap gap-2 custom-radio">${options.replace(/{{index}}/g, index)}</div>
                        </td>
                        <td class="px-4 py-3">
                            <input type="text" class="w-full p-1 border border-gray-300 rounded-md text-sm">
                        </td>
                    </tr>`;
                });
                html += subItem.isSingle ? `</tbody></table>` : `</tbody></table></div></div>`;
                return html;
            }
            function generateRekapTableHead(tahap) {
                 return tahap === 1 
                    ? `<thead class="bg-gray-100 text-xs uppercase font-medium text-gray-600">
                        <tr>
                            <th rowspan="2" class="p-2 border">No.</th>
                            <th rowspan="2" class="p-2 border text-left">Bagian-bagian yang diperiksa</th>
                            <th colspan="4" class="p-2 border">Kondisi *)</th>
                            <th rowspan="2" class="p-2 border">Keterangan</th>
                        </tr>
                        <tr>
                            <th class="p-2 border font-medium">Baik</th>
                            <th class="p-2 border font-medium">Rusak (Lkp)</th>
                            <th class="p-2 border font-medium">Rusak (Tdk. Lkp)</th>
                            <th class="p-2 border font-medium">Tidak Ada</th>
                        </tr>
                    </thead>`
                    : `<thead class="bg-gray-100 text-xs uppercase font-medium text-gray-600">
                        <tr>
                            <th rowspan="2" class="p-2 border">No.</th>
                            <th rowspan="2" class="p-2 border text-left">Bagian-bagian yang diperiksa</th>
                            <th colspan="3" class="p-2 border">Kondisi *)</th>
                            <th rowspan="2" class="p-2 border">Keterangan</th>
                        </tr>
                        <tr>
                            <th class="p-2 border font-medium">Baik/Lancar</th>
                            <th class="p-2 border font-medium">Rusak/Tdk Lancar</th>
                            <th class="p-2 border font-medium">Tidak Hidup</th>
                        </tr>
                    </thead>`;
            }
            function generateRekapTBody(tbody, data, tahap) {
                let html = '';
                const romanNumerals = ['I', 'II', 'III', 'IV', 'V'];
                const rekapOptions = tahap === 1
                    ? `<td class="rekap-radio-cell">
                        <input type="radio" id="{{rekapName}}-baik" name="{{rekapName}}" disabled>
                    </td>
                    <td class="rekap-radio-cell">
                        <input type="radio" id="{{rekapName}}-rusak-lkp" name="{{rekapName}}" disabled>
                    </td>
                    <td class="rekap-radio-cell">
                        <input type="radio" id="{{rekapName}}-rusak-tdk-lkp" name="{{rekapName}}" disabled>
                    </td>
                    <td class="rekap-radio-cell">
                        <input type="radio" id="{{rekapName}}-tidak-ada" name="{{rekapName}}" disabled>
                    </td>`
                    : `<td class="rekap-radio-cell">
                        <input type="radio" id="{{rekapName}}-baik" name="{{rekapName}}" disabled>
                    </td>
                    <td class="rekap-radio-cell">
                        <input type="radio" id="{{rekapName}}-rusak" name="{{rekapName}}" disabled>
                    </td>
                    <td class="rekap-radio-cell">
                        <input type="radio" id="{{rekapName}}-mati" name="{{rekapName}}" disabled>
                    </td>`;
                Object.entries(data).forEach(([mainKey, mainValue]) => {
                    const colspan = tahap === 1 ? 7 : 6;
                    html += `<tr class="bg-gray-50 font-semibold">
                        <td colspan="${colspan}" class="p-2 border">${mainKey}. ${mainValue.title}</td>
                    </tr>`;
                    mainValue.items.forEach((subItem, index) => {
                        const rekapName = `rekap-t${tahap}-${subItem.id}`;
                        let rowAttributes = (mainKey === 'C') ? `class="rekap-mixer-row-t${tahap}" id="rekap-row-t${tahap}-${subItem.id}" style="display: none;"` : '';
                        html += `<tr ${rowAttributes}>
                            <td class="p-2 border text-center">${romanNumerals[index]}</td>
                            <td class="p-2 border">${subItem.title.replace(/\s\*\*\*\)/, '')}</td>${rekapOptions.replace(/{{rekapName}}/g, rekapName).replace(/<td/g, '<td class="rekap-radio-cell border"')}
                            <td class="p-2 border">
                                <textarea class="w-full text-sm p-1 border-gray-200 rounded" rows="1"></textarea>
                            </td>
                        </tr>`;
                    });
                });
                tbody.innerHTML = html;
            }
            function generateTahap3HTML(container) {
                let t3HTML = tahap3Data.t3_items.map(item => `<tr>
                    <td class="p-2 border">${item.pengujian}</td>
                    <td class="p-2 border" data-max="${item.max}">${item.max}</td>
                    <td class="p-2 border">
                        <input type="number" class="w-full p-1 border-gray-200 rounded result-input-t3" data-row="${item.rowId}">
                    </td>
                    <td class="p-2 border text-center">
                        <div class="flex items-center justify-center gap-2">
                            <input type="radio" name="${item.rowId}_spec" id="${item.rowId}_spec_sesuai" disabled>
                            <label for="${item.rowId}_spec_sesuai" id="${item.rowId}_label_sesuai">Sesuai</label>
                        </div>
                    </td>
                    <td class="p-2 border text-center">
                        <div class="flex items-center justify-center gap-2">
                            <input type="radio" name="${item.rowId}_spec" id="${item.rowId}_spec_tidak" disabled>
                            <label for="${item.rowId}_spec_tidak" id="${item.rowId}_label_tidak">Tidak</label>
                        </div>
                    </td>
                </tr>`).join('');
                container.innerHTML = `<div class="bg-gray-50 rounded-lg border">
                    <div class="font-semibold cursor-pointer p-4 flex justify-between items-center" data-toggle-parent="t3-content">
                        <span>Tahap III: Pemeriksaan Kelaikan Operasi</span>
                        <svg class="w-5 h-5 transform transition-transform chevron-icon" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
                        </svg>
                    </div>
                    <div class="collapsible-content" id="t3-content">
                        <div class="p-4 border-t overflow-x-auto">
                            <h3 class="font-semibold text-md mb-3">Hasil Pengujian Parameter Keseragaman Beton</h3>
                            <table class="min-w-full bg-white border">
                                <thead class="bg-gray-100 text-xs uppercase">
                                    <tr>
                                        <th class="p-2 border text-left">Pengujian</th>
                                        <th class="p-2 border text-left">Perbedaan Maksimum Diizinkan</th>
                                        <th class="p-2 border text-left">Hasil Pengujian</th>
                                        <th class="p-2 border text-center" colspan="2">Peninjauan Spesifikasi</th>
                                    </tr>
                                </thead>
                                <tbody class="text-sm">${t3HTML}</tbody>
                            </table>
                            <div class="mt-4"><h4 class="font-semibold text-sm mb-2">Kesimpulan Pemeriksaan Keseragaman Beton</h4><div class="flex gap-4 custom-radio"><input type="radio" name="conclusion_tahap3_keseragaman" id="t3_keseragaman_sesuai"><label for="t3_keseragaman_sesuai">Sesuai</label><input type="radio" name="conclusion_tahap3_keseragaman" id="t3_keseragaman_tidak"><label for="t3_keseragaman_tidak">Tidak Sesuai</label></div></div>
                             <div class="mt-4 text-right"><button class="print-rekap-btn text-sm bg-red-600 text-white py-2 px-4 rounded-md hover:bg-red-700" data-tahap="3-1"><i class="fas fa-print mr-2"></i>Cetak PDF</button></div>
                        </div>
                    </div>
                </div>
                <div class="bg-gray-50 rounded-lg border">
                    <div class="font-semibold cursor-pointer p-4 flex justify-between items-center" data-toggle-parent="produksi-content">
                        <span>Pemeriksaan Kelaikan Produksi</span>
                        <svg class="w-5 h-5 transform transition-transform chevron-icon" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7" />
                        </svg>
                    </div>
                    <div class="collapsible-content" id="produksi-content">
                        <div class="p-4 border-t overflow-x-auto">
                            <h3 class="font-semibold text-md mb-3">Hasil Pengujian Produk Mutu Campuran Beton Semen</h3>
                            <table class="min-w-full bg-white border">
                                <thead class="bg-gray-100 text-xs uppercase">
                                    <tr>
                                        <th class="p-2 border text-left">Pengujian</th>
                                        <th class="p-2 border text-left">Spesifikasi Teknis</th>
                                        <th class="p-2 border text-left">Hasil Pengukuran</th>
                                        <th class="p-2 border text-center" colspan="2">Peninjauan Spesifikasi</th>
                                    </tr>
                                </thead>
                                <tbody class="text-sm">
                                    <tr class="bg-gray-50 font-semibold">
                                        <td colspan="5" class="p-2 border">Parameter Beton Segar</td>
                                    </tr>
                                    <tr>
                                        <td class="p-2 border">Slump (mm)</td>
                                        <td class="p-2 border">
                                            <input placeholder="Min-Maks (cth: 75-100)" type="text" class="w-full p-1 border-gray-200 rounded spec-input" data-row="prod_1">
                                        </td>
                                        <td class="p-2 border">
                                            <input placeholder="Hasil" type="number" class="w-full p-1 border-gray-200 rounded result-input" data-row="prod_1">
                                        </td>
                                        <td class="p-2 border text-center">
                                            <div class="flex items-center justify-center gap-2">
                                                <input type="radio" name="prod_1_spec" id="prod_1_spec_sesuai" disabled>
                                                <label for="prod_1_spec_sesuai" id="prod_1_label_sesuai">Sesuai</label>
                                            </div>
                                        </td>
                                        <td class="p-2 border text-center">
                                            <div class="flex items-center justify-center gap-2">
                                                <input type="radio" name="prod_1_spec" id="prod_1_spec_tidak" disabled>
                                                <label for="prod_1_spec_tidak" id="prod_1_label_tidak">Tidak</label>
                                            </div>
                                        </td>
                                    </tr>
                                    <tr class="bg-gray-50 font-semibold">
                                        <td colspan="5" class="p-2 border">Parameter Beton Keras</td>
                                    </tr>
                                    <tr>
                                        <td class="p-2 border">Kuat Lentur</td>
                                        <td class="p-2 border">
                                            <input placeholder="Min-Maks (cth: 4.0-5.0)" type="text" class="w-full p-1 border-gray-200 rounded spec-input" data-row="prod_2">
                                        </td>
                                        <td class="p-2 border">
                                            <input placeholder="Hasil" type="number" step="0.1" class="w-full p-1 border-gray-200 rounded result-input" data-row="prod_2">
                                        </td>
                                        <td class="p-2 border text-center">
                                            <div class="flex items-center justify-center gap-2">
                                                <input type="radio" name="prod_2_spec" id="prod_2_spec_sesuai" disabled>
                                                <label for="prod_2_spec_sesuai" id="prod_2_label_sesuai">Sesuai</label>
                                            </div>
                                        </td>
                                        <td class="p-2 border text-center">
                                            <div class="flex items-center justify-center gap-2">
                                                <input type="radio" name="prod_2_spec" id="prod_2_spec_tidak" disabled>
                                                <label for="prod_2_spec_tidak" id="prod_2_label_tidak">Tidak</label>
                                            </div>
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                            <div class="mt-4">
                                <h4 class="font-semibold text-sm mb-2">Kesimpulan Pemeriksaan Mutu Campuran Produksi</h4>
                                <div class="flex gap-4 custom-radio">
                                    <input type="radio" name="conclusion_produksi" id="prod_laik">
                                    <label for="prod_laik">Laik</label>
                                    <input type="radio" name="conclusion_produksi" id="prod_tidak_laik">
                                    <label for="prod_tidak_laik">Tidak Laik</label>
                                </div>
                            </div>
                            <div class="mt-4 text-right">
                                <button class="print-rekap-btn text-sm bg-red-600 text-white py-2 px-4 rounded-md hover:bg-red-700" data-tahap="3-2">
                                    <i class="fas fa-print mr-2"></i>Cetak PDF</button>
                            </div>
                        </div>
                    </div>
                </div>`;
            }
            function initializeForm() {
                if (formInitialized) return;
                formPage.innerHTML = generateFullFormHTML();
                const formContainer = document.getElementById('inspection-form');
                const TAHAP1_CONTAINER = formContainer.querySelector('#tahap1-details');
                const TAHAP2_CONTAINER = formContainer.querySelector('#tahap2-details');
                const TAHAP3_CONTAINER = formContainer.querySelector('#tahap3-details');
                const prevBtn = formContainer.querySelector('#prev-btn');
                const nextBtn = formContainer.querySelector('#next-btn');
                const backToDashboardBtn = formPage.querySelector('#back-to-dashboard-btn');
                const formSections = formContainer.querySelectorAll('.form-section');
                const stepDots = formPage.querySelectorAll('.step-indicator div[id^="step-dot-"]');
                const stepTexts = formPage.querySelectorAll('.step-indicator p[id^="step-text-"]');
                let currentStep = 1;
                const totalSteps = formSections.length;
                function updateFormState() {
                    formSections.forEach((section, index) => section.classList.toggle('active', (index + 1) === currentStep));
                    stepDots.forEach((dot, index) => {
                        const step = index + 1;
                        dot.className = 'mx-auto w-8 h-8 rounded-full border-2 flex items-center justify-center transition-all duration-300';
                        stepTexts[index].className = 'text-xs mt-1 transition-all duration-300';
                        if (step < currentStep) {
                            dot.classList.add('bg-blue-600', 'border-blue-600', 'text-white');
                            stepTexts[index].classList.add('text-blue-700', 'font-semibold');
                        } else if (step === currentStep) {
                            dot.classList.add('bg-blue-600', 'border-blue-600', 'text-white', 'scale-110');
                            stepTexts[index].classList.add('text-blue-700', 'font-semibold');
                        } else {
                            dot.classList.add('bg-gray-200', 'border-gray-300', 'text-gray-500');
                            stepTexts[index].classList.add('text-gray-500');
                        }
                    });
                    prevBtn.disabled = currentStep === 1;
                    nextBtn.style.display = currentStep === totalSteps ? 'none' : 'inline-block';
                }
                function validateCurrentStep() {
                    let isValid = true;
                    const currentSection = formContainer.querySelector(`.form-section.active`);
                    currentSection.querySelectorAll('.validation-error').forEach(el => el.classList.remove('validation-error'));
                    const visibleInputs = currentSection.querySelectorAll('input[required]:not(:disabled), textarea[required]:not(:disabled), select[required]:not(:disabled)');
                    visibleInputs.forEach(input => {
                        const isVisible = input.offsetParent !== null;
                        if (isVisible && !input.value) {
                            isValid = false;
                            input.classList.add('validation-error');
                        }
                    });
                    const radioGroups = {};
                    const allRadios = currentSection.querySelectorAll('input[type="radio"]:not([name^="conclusion_"])');
                    allRadios.forEach(radio => {
                        const container = radio.closest('tr');
                        if (container && container.offsetParent !== null) {
                             if (!radioGroups[radio.name]) {
                                radioGroups[radio.name] = {
                                    elements: [],
                                    container: container
                                };
                            }
                            radioGroups[radio.name].elements.push(radio);
                        }
                    });
                    for (const groupName in radioGroups) {
                        if (radioGroups.hasOwnProperty(groupName)) {
                            const isGroupChecked = radioGroups[groupName].elements.some(radio => radio.checked);
                            if (!isGroupChecked) {
                                isValid = false;
                                if (radioGroups[groupName].container) {
                                    radioGroups[groupName].container.classList.add('validation-error');
                                }
                            }
                        }
                    }
                    let alertEl = currentSection.querySelector('.step-validation-alert');
                    if (!alertEl && (currentStep > 1 && currentStep < 5)) {
                         alertEl = document.createElement('div');
                         alertEl.className = 'alert-message step-validation-alert';
                         alertEl.textContent = 'Harap isi semua bagian yang wajib diisi (ditandai dengan warna merah).';
                         const conclusionDiv = currentSection.querySelector('div[id^="conclusion-step-"]');
                         if (conclusionDiv) {
                            conclusionDiv.parentNode.insertBefore(alertEl, conclusionDiv);
                         }
                    }
                    if (!isValid) {
                       if(alertEl) alertEl.style.display = 'block';
                    } else {
                       if(alertEl) alertEl.style.display = 'none';
                    }
                    return isValid;
                }
                generateForm(TAHAP1_CONTAINER, tahap1Data, 1);
                generateForm(TAHAP2_CONTAINER, tahap2Data, 2);
                generateTahap3HTML(TAHAP3_CONTAINER);
                // Navigation Logic
                nextBtn.addEventListener('click', () => {
                    let canProceed = true;
                    if (currentStep === 1 && !validateCurrentStep()) {
                        canProceed = false;
                    } else if (currentStep === 2) {
                        if (!validateCurrentStep()) canProceed = false;
                        else {
                            const conclusion = formContainer.querySelector('input[name="conclusion_tahap1"]:checked');
                            if (!conclusion || conclusion.value !== 'pass') {
                                formContainer.querySelector('#alert-step-2').style.display = 'block';
                                canProceed = false;
                            } else {
                                formContainer.querySelector('#alert-step-2').style.display = 'none';
                            }
                        }
                    } else if (currentStep === 3) {
                        if (!validateCurrentStep()) canProceed = false;
                        else {
                            const conclusion = formContainer.querySelector('input[name="conclusion_tahap2"]:checked');
                            if (!conclusion || conclusion.value !== 'pass') {
                                formContainer.querySelector('#alert-step-3').style.display = 'block';
                                canProceed = false;
                            } else {
                                 formContainer.querySelector('#alert-step-3').style.display = 'none';
                            }
                        }
                    } else if (currentStep === 4) {
                        if (!validateCurrentStep()) canProceed = false;
                    }
                    if (canProceed && currentStep < totalSteps) {
                        currentStep++;
                        updateFormState();
                    }
                });
                prevBtn.addEventListener('click', () => {
                    if (currentStep > 1) {
                        currentStep--;
                        updateFormState();
                    }
                });
                backToDashboardBtn.addEventListener('click', () => {
                    showPage('dashboard-page');
                });
                updateFormState();
                initializeCollapsibles();
                initializeFormEventListeners(formContainer);
                formInitialized = true;
            }
            function initializeCollapsibles() {
                const container = document.getElementById('form-page');
                if(!container) return;
                container.querySelectorAll('[data-toggle-parent]').forEach(header => {
                    const contentId = header.dataset.toggleParent;
                    const content = document.getElementById(contentId)
                    if(content){
                        content.classList.add('open');
                        header.querySelector('.chevron-icon').classList.add('rotate-180');
                    }
                });
            }
            function initializeFormEventListeners(formContainer) {
                if (!formContainer) return;
                const priorityT1 = {'baik': 1, 'rusak-lkp': 2, 'rusak-tdk-lkp': 3, 'tidak-ada': 4};
                const priorityT2 = {'baik': 1, 'rusak': 2, 'mati': 3};
                function rekapLogic(detailSelector, rekapName, priority) {
                    const detailRadios = formContainer.querySelectorAll(detailSelector);
                    let worstStatusValue = 1; 
                    let worstStatusKey = Object.keys(priority).find(k => priority[k] === 1);
                    let allChecked = Array.from(new Set([...detailRadios].map(r => r.name))).every(name => formContainer.querySelector(`input[name="${name}"]:checked`));
                    if (allChecked) {
                        new Set([...detailRadios].map(r => r.name)).forEach(radioName => {
                            const checkedRadio = formContainer.querySelector(`input[name="${radioName}"]:checked`);
                            if (priority[checkedRadio.value] > worstStatusValue) {
                                worstStatusValue = priority[checkedRadio.value];
                                worstStatusKey = checkedRadio.value;
                            }
                        });
                    }
                    formContainer.querySelectorAll(`input[name="${rekapName}"]`).forEach(r => r.checked = false);
                    if (allChecked) {
                        const targetRekapRadio = formContainer.querySelector(`#${rekapName}-${worstStatusKey.replace(/\s+/g, '-')}`);
                        if (targetRekapRadio) targetRekapRadio.checked = true;
                    }
                };
                formContainer.addEventListener('change', function(e) {
                    const target = e.target;
                    const container = target.closest('tr') || target.closest('div.grid');
                    if (container && container.classList.contains('validation-error')) {
                        container.classList.remove('validation-error');
                    }
                    if (formContainer.querySelector('#step-2').contains(target)) {
                        if (target.dataset.rekapGroup) rekapLogic(`input[data-rekap-group="${target.dataset.rekapGroup}"]`, target.dataset.rekapGroup, priorityT1);
                        if (target.id === 'mixer-type-select-t1') handleMixerSelection(1, target.value);
                    }
                    if (formContainer.querySelector('#step-3').contains(target)) {
                        if (target.dataset.rekapGroup) rekapLogic(`input[data-rekap-group="${target.dataset.rekapGroup}"]`, target.dataset.rekapGroup, priorityT2);
                         if (target.id === 'mixer-type-select-t2') handleMixerSelection(2, target.value);
                    }
                });
                function handleMixerSelection(tahap, selectedType) {
                    document.querySelectorAll(`#tahap${tahap}-details .mixer-type-section-t${tahap}`).forEach(section => section.style.display = 'none');
                    document.querySelectorAll(`#rekap-t${tahap}-tbody .rekap-mixer-row-t${tahap}`).forEach(row => row.style.display = 'none');
                    if (selectedType) {
                        const sectionToShow = document.getElementById(`t${tahap}-mixer-type-${selectedType}`);
                        if (sectionToShow) sectionToShow.style.display = 'block';
                        const rekapRowToShow = document.getElementById(`rekap-row-t${tahap}-${selectedType}`);
                        if (rekapRowToShow) rekapRowToShow.style.display = 'table-row';
                    }
                }
                formContainer.addEventListener('click', function(event) {
                    const parentToggle = event.target.closest('[data-toggle-parent]');
                    const groupToggle = event.target.closest('[data-toggle-group]');
                    if (parentToggle) {
                        const content = document.getElementById(parentToggle.dataset.toggleParent);
                        if(content) {
                            content.classList.toggle('open');
                            parentToggle.querySelector('.chevron-icon').classList.toggle('rotate-180');
                        }
                    } else if (groupToggle) {
                        formContainer.querySelectorAll(`.collapsible-content.${groupToggle.dataset.toggleGroup}`).forEach(item => item.classList.toggle('open'));
                        groupToggle.querySelector('.chevron-icon').classList.toggle('rotate-180');
                    } else if (event.target.classList.contains('print-rekap-btn')) {
                        const tahap = event.target.dataset.tahap;
                        printRekap(tahap);
                    }
                });
                formContainer.querySelector('#tahap3-details').addEventListener('input', (e) => {
                    const rowId = e.target.dataset.row;
                    if (!rowId) return;
                    if (e.target.classList.contains('result-input-t3')) handleT3Validation(rowId);
                    else if (e.target.classList.contains('spec-input') || e.target.classList.contains('result-input')) handleProductionValidation(rowId);
                });
                 function handleT3Validation(rowIdentifier) {
                    const resultInput = formContainer.querySelector(`.result-input-t3[data-row="${rowIdentifier}"]`);
                    const maxCell = resultInput.closest('tr').querySelector('[data-max]');
                    const sesuaiRadio = formContainer.querySelector(`#${rowIdentifier}_spec_sesuai`);
                    const tidakRadio = formContainer.querySelector(`#${rowIdentifier}_spec_tidak`);
                    const sesuaiLabel = formContainer.querySelector(`#${rowIdentifier}_label_sesuai`);
                    const tidakLabel = formContainer.querySelector(`#${rowIdentifier}_label_tidak`);
                    sesuaiLabel.classList.remove('text-green-600','font-semibold');
                    tidakLabel.classList.remove('text-red-600','font-semibold');
                    const resultValue = parseFloat(resultInput.value);
                    const maxValue = parseFloat(maxCell.dataset.max);
                    if (isNaN(resultValue) || isNaN(maxValue)) {
                        sesuaiRadio.checked = false;
                        tidakRadio.checked = false;
                        return;
                    }
                    if (resultValue <= maxValue) {
                        sesuaiRadio.checked = true;
                        sesuaiLabel.classList.add('text-green-600','font-semibold');
                    } else {
                        tidakRadio.checked = true;
                        tidakLabel.classList.add('text-red-600','font-semibold');
                    }
                }
                function handleProductionValidation(rowIdentifier) {
                    const specInput = formContainer.querySelector(`.spec-input[data-row="${rowIdentifier}"]`);
                    const resultInput = formContainer.querySelector(`.result-input[data-row="${rowIdentifier}"]`);
                    const sesuaiRadio = formContainer.querySelector(`#${rowIdentifier}_spec_sesuai`);
                    const tidakRadio = formContainer.querySelector(`#${rowIdentifier}_spec_tidak`);
                    const sesuaiLabel = formContainer.querySelector(`#${rowIdentifier}_label_sesuai`);
                    const tidakLabel = formContainer.querySelector(`#${rowIdentifier}_label_tidak`);
                    sesuaiLabel.classList.remove('text-green-600','font-semibold');
                    tidakLabel.classList.remove('text-red-600','font-semibold');
                    const specValue = specInput.value;
                    const resultValue = parseFloat(resultInput.value);
                    if (!specValue || isNaN(resultValue)) {
                        sesuaiRadio.checked = false;
                        tidakRadio.checked = false;
                        return;
                    }
                    const specParts = specValue.split('-').map(s => parseFloat(s.trim()));
                    if (specParts.length !== 2 || isNaN(specParts[0]) || isNaN(specParts[1])) {
                        sesuaiRadio.checked = false;
                        tidakRadio.checked = false;
                        return;
                    }
                    const [min, max] = specParts;
                    if (resultValue >= min && resultValue <= max) {
                        sesuaiRadio.checked = true;
                        sesuaiLabel.classList.add('text-green-600','font-semibold');
                    } else {
                        tidakRadio.checked = true;
                        tidakLabel.classList.add('text-red-600','font-semibold');
                    }
                }
                 function printRekap(tahap) {
                    const { jsPDF } = window.jspdf;
                    const doc = new jsPDF();
                    let title = '';
                    let body = [];
                    if(tahap === '1' || tahap === '2') {
                        title = `Rekapitulasi Pemeriksaan Tahap ${tahap === '1' ? 'I' : 'II'}`;
                        const table = document.getElementById(`rekap-t${tahap}-tbody`);
                        const headerRow1 = [
                            { content: 'No.', rowSpan: 2 },
                            { content: 'Bagian-bagian yang diperiksa', rowSpan: 2 },
                            { content: 'Kondisi *)', colSpan: tahap === '1' ? 4 : 3, styles: { halign: 'center' } },
                            { content: 'Keterangan', rowSpan: 2 },
                        ];
                        const headerRow2 = tahap === '1' 
                            ? ['Baik', 'Rusak (Lkp)', 'Rusak (Tdk. Lkp)', 'Tidak Ada']
                            : ['Baik/Lancar', 'Rusak/Tdk Lancar', 'Tidak Hidup'];
                        Array.from(table.rows).forEach(row => {
                            if (row.style.display === 'none') return;
                            if (row.classList.contains('bg-gray-50')) {
                                body.push([{ content: row.cells[0].innerText, colSpan: tahap === '1' ? 7 : 6, styles: { fontStyle: 'bold', fillColor: [243, 244, 246] } }]);
                            } else {
                                let rowData = [];
                                rowData.push(row.cells[0].innerText);
                                rowData.push(row.cells[1].innerText);
                                for (let i = 2; i < row.cells.length - 1; i++) {
                                    rowData.push(row.cells[i].querySelector('input[type="radio"]:checked') ? 'X' : '');
                                }
                                rowData.push(row.cells[row.cells.length - 1].querySelector('textarea').value);
                                body.push(rowData);
                            }
                        });
                        doc.autoTable({
                            head: [headerRow1, headerRow2], body: body,
                            didDrawPage: data => doc.text(title, data.settings.margin.left, 15),
                            margin: { top: 20 }
                        });
                    } else if (tahap === '3-1' || tahap === '3-2') {
                        const isTahap31 = tahap === '3-1';
                        const tableContainer = document.getElementById(isTahap31 ? 't3-content' : 'produksi-content');
                        title = tableContainer.querySelector('h3').innerText;
                        const table = tableContainer.querySelector('table');
                        const head = Array.from(table.querySelectorAll('thead th')).map(th => th.innerText);
                        Array.from(table.querySelectorAll('tbody tr')).forEach(row => {
                            if (row.classList.contains('bg-gray-50')) {
                                body.push([{ content: row.cells[0].innerText, colSpan: head.length, styles: { fontStyle: 'bold', fillColor: [243, 244, 246] } }]);
                            } else {
                                let rowData = [
                                    row.cells[0].innerText,
                                    row.cells[1].innerText,
                                    row.cells[2].querySelector('input').value,
                                    row.cells[3].querySelector('input').checked ? 'Sesuai' : (row.cells[4].querySelector('input').checked ? 'Tidak' : '')
                                ];
                                body.push(rowData);
                            }
                        });
                        const finalHead = isTahap31
                            ? [head[0], head[1], head[2], 'Peninjauan']
                            : [head[0], head[1], head[2], 'Peninjauan'];
                        doc.autoTable({
                            head: [finalHead], body: body,
                            didDrawPage: data => doc.text(title, data.settings.margin.left, 15),
                             margin: { top: 20 }
                        });
                    }
                    doc.save(`${title.replace(/ /g, '_')}.pdf`);
                }
            }
            // --- PAGE NAVIGATION ---
            function showPage(pageId) {
                dashboardPage.classList.remove('active');
                formPage.classList.remove('active');
                document.getElementById(pageId).classList.add('active');
                window.scrollTo(0, 0);
            }
            startNewInspectionBtn.addEventListener('click', () => {
                initializeForm();
                showPage('form-page');
            });
            navDashboard.addEventListener('click', (e) => {
                e.preventDefault();
                showPage('dashboard-page');
            });
            // --- REGISTRATION MODAL LOGIC ---
            const modal = document.getElementById('registration-modal');
            const openModalBtn = document.getElementById('open-registration-modal-btn');
            const closeModalBtn = document.getElementById('close-modal-btn');
            const cancelModalBtn = document.getElementById('cancel-registration-btn');
            const registrationForm = document.getElementById('registration-form');
            const navRegistration = document.getElementById('nav-registration');
            const photoInput = document.getElementById('reg-photos');
            const photoPreviewContainer = document.getElementById('photo-preview-container');
            const openModal = () => modal.classList.add('open');
            const closeModal = () => {
                modal.classList.remove('open');
                registrationForm.reset();
                photoPreviewContainer.innerHTML = '';
            }
            openModalBtn.addEventListener('click', openModal);
            navRegistration.addEventListener('click', (e) => {
                e.preventDefault();
                openModal();
            });
            closeModalBtn.addEventListener('click', closeModal);
            cancelModalBtn.addEventListener('click', closeModal);
            modal.addEventListener('click', (e) => {
                if(e.target === modal) closeModal();
            });
            registrationForm.addEventListener('submit', (e) => {
                e.preventDefault();
                alert('Pendaftaran berhasil disimulasikan!');
                closeModal();
            });
            photoInput.addEventListener('change', () => {
                photoPreviewContainer.innerHTML = '';
                const files = Array.from(photoInput.files).slice(0, 10);
                files.forEach(file => {
                    if (file.size > 2 * 1024 * 1024) {
                        alert(`File ${file.name} melebihi batas 2MB.`);
                        return;
                    }
                    if (file.type.startsWith('image/')) {
                        const reader = new FileReader();
                        reader.onload = e => {
                            const div = document.createElement('div');
                            div.className = 'relative';
                            div.innerHTML = `<img src="${e.target.result}" class="w-full h-24 object-cover rounded-md">`;
                            photoPreviewContainer.appendChild(div);
                        };
                        reader.readAsDataURL(file);
                    }
                });
            });
            document.getElementById('get-gps-btn').addEventListener('click', () => {
                if (navigator.geolocation) {
                    navigator.geolocation.getCurrentPosition(position => {
                        const lat = position.coords.latitude.toFixed(6);
                        const lon = position.coords.longitude.toFixed(6);
                        document.getElementById('reg-gps').value = `${lat}, ${lon}`;
                    }, () => {
                        alert('Tidak dapat mengambil lokasi. Pastikan izin lokasi telah diberikan.');
                    });
                } else {
                    alert('Geolocation tidak didukung oleh browser ini.');
                }
            });
        });
    </script>
</body>
</html>
