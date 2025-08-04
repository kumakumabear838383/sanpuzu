<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ふるさと納税 返礼品 時系列分析レポート - 自治体フィルタ対応版</title>

    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 20px;
            background-color: #f4f7f6;
            color: #333;
        }
        h1 {
            text-align: center;
            color: #2c3e50;
            margin-bottom: 30px;
            font-size: 2.5em;
        }
        h2 {
            text-align: center;
            color: #34495e;
            margin-top: 40px;
            margin-bottom: 20px;
            font-size: 1.8em;
        }
        
        .input-section {
            text-align: center;
            margin-bottom: 30px;
            padding: 20px;
            background-color: #ffffff;
            border-radius: 8px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.08);
        }
        .input-section label {
            font-size: 1.2em;
            margin-right: 10px;
        }
        .input-section input[type="file"] {
            padding: 8px;
            border: 1px solid #ccc;
            border-radius: 5px;
            background-color: #f9f9f9;
        }
        .input-section button {
            padding: 10px 20px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 1em;
            margin-left: 10px;
        }
        .input-section button:hover {
            background-color: #45a049;
        }
        #fileLoadStatus {
            margin-top: 10px;
            font-weight: bold;
            color: #333;
        }
        .city-info {
            margin-top: 15px;
            padding: 10px;
            background-color: #e8f4f8;
            border-radius: 5px;
            font-size: 14px;
        }
        
        .select-container {
            text-align: center;
            margin-bottom: 20px;
            display: flex;
            justify-content: center;
            gap: 20px;
            flex-wrap: wrap;
            padding: 20px;
            background-color: #ffffff;
            border-radius: 8px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.08);
        }
        .select-container div {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        .select-container div.analysis-target-container,
        .select-container div.item-selector-container,
        .select-container div.keyword-input-container,
        .select-container div.comparison-container,
        .select-container div.city-filter-container {
            align-items: flex-start;
            flex-direction: column;
            flex-basis: 100%;
            max-width: 100%;
        }
        label {
            font-size: 1.1em;
            color: #555;
        }
        select, input[type="text"] {
            padding: 8px 12px;
            border: 1px solid #ccc;
            border-radius: 5px;
            font-size: 1em;
            background-color: #fff;
            cursor: pointer;
            min-width: 150px;
        }
        input[type="text"] {
            cursor: text;
        }
        
        .analysis-target-container {
            margin: 15px 0;
        }
        
        .analysis-target-container label {
            display: block;
            margin-bottom: 10px;
            font-weight: bold;
        }
        
        #analysisTargetSelector {
            width: 200px;
            margin: 0 auto;
        }
        
        .comparison-container {
            margin: 15px 0;
        }
        
        .comparison-container label {
            display: block;
            margin-bottom: 10px;
            font-weight: bold;
        }
        
        #comparisonMode {
            width: 250px;
            margin: 0 auto;
        }
        
        #targetCitySelector {
            width: 200px;
            margin: 10px auto;
        }

        /* 複数自治体選択用のスタイル */
        #multiCitySelector {
            margin-top: 15px;
            padding: 15px;
            background-color: #f0f8ff;
            border-radius: 8px;
            border-left: 4px solid #2196f3;
        }

        #multiCitySelector label {
            display: block;
            margin-bottom: 10px;
            font-weight: bold;
            color: #1976d2;
        }

        #cityMultiSelect {
            width: 100%;
            max-width: 600px;
            margin: 0 auto;
        }

        /* 自治体フィルタ用のスタイル */
        .city-filter-container {
            margin: 15px 0;
        }
        
        .city-filter-container label {
            display: block;
            margin-bottom: 10px;
            font-weight: bold;
        }
        
        #cityFilterSelector {
            margin-top: 15px;
            padding: 15px;
            background-color: #f0fff0;
            border-radius: 8px;
            border-left: 4px solid #4CAF50;
        }

        #cityFilterSelector label {
            display: block;
            margin-bottom: 10px;
            font-weight: bold;
            color: #2d5016;
        }

        #cityFilterMultiSelect {
            width: 100%;
            max-width: 600px;
            margin: 0 auto;
        }
        
        .keyword-input-container {
            margin: 15px 0;
        }
        
        .keyword-input-container label {
            display: block;
            margin-bottom: 10px;
            font-weight: bold;
        }
        
        #keywordInput {
            width: 100%;
            max-width: 400px;
            margin: 0 auto;
            padding: 12px;
            font-size: 16px;
            border: 2px solid #ddd;
            border-radius: 8px;
        }
        
        #keywordInput:focus {
            border-color: #4CAF50;
            outline: none;
        }
        
        .keyword-stats {
            margin-top: 10px;
            padding: 15px;
            background-color: #f8f9fa;
            border-radius: 8px;
            font-size: 14px;
            color: #666;
            border-left: 4px solid #4CAF50;
        }

        .keyword-stats strong {
            color: #333;
        }

        .keyword-stats div {
            display: flex;
            align-items: center;
            gap: 8px;
        }

        /* 高度な検索機能のスタイル */
        .advanced-search-container {
            margin: 15px 0;
            padding: 20px;
            background-color: #f8f9fa;
            border-radius: 8px;
            border-left: 4px solid #007bff;
        }

        .advanced-search-container h3 {
            margin: 0 0 15px 0;
            color: #007bff;
            font-size: 1.2em;
        }

        .search-row {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-bottom: 15px;
            flex-wrap: wrap;
        }

        .search-row label {
            min-width: 80px;
            font-weight: bold;
            color: #495057;
        }

        .search-row input[type="text"] {
            flex: 1;
            min-width: 200px;
            padding: 8px 12px;
            border: 1px solid #ced4da;
            border-radius: 4px;
        }

        .search-row select {
            min-width: 80px;
            padding: 8px 12px;
            border: 1px solid #ced4da;
            border-radius: 4px;
            background-color: #fff;
        }

        .search-condition-row {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-bottom: 15px;
        }

        .search-condition-row label {
            font-weight: bold;
            color: #495057;
        }

        .search-stats {
            margin-top: 15px;
            padding: 10px;
            background-color: #e9ecef;
            border-radius: 4px;
            font-size: 14px;
            color: #495057;
        }

        .search-buttons {
            display: flex;
            gap: 10px;
            margin-top: 15px;
        }

        .search-buttons button {
            padding: 8px 16px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 14px;
        }

        .search-buttons .btn-primary {
            background-color: #007bff;
            color: white;
        }

        .search-buttons .btn-secondary {
            background-color: #6c757d;
            color: white;
        }

        .search-buttons button:hover {
            opacity: 0.9;
        }
        
        .year-comparison-section {
            margin: 15px 0;
            padding: 15px;
            background-color: #e8f4f8;
            border-radius: 8px;
            border-left: 4px solid #3498db;
        }
        
        .year-comparison-section h3 {
            margin: 0 0 10px 0;
            color: #2c3e50;
            font-size: 1.1em;
        }
        
        .year-selector-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
            gap: 10px;
            margin-top: 10px;
        }
        
        .year-checkbox {
            display: flex;
            align-items: center;
            gap: 5px;
        }
        
        .year-checkbox input[type="checkbox"] {
            width: auto;
            min-width: auto;
        }
        
        .year-checkbox label {
            font-size: 0.9em;
            margin: 0;
        }
        
        /* Select2のカスタムスタイル */
        .item-selector-container {
            margin: 15px 0;
        }
        
        .item-selector-container label {
            display: block;
            margin-bottom: 10px;
            font-weight: bold;
        }
        
        .select2-container {
            width: 100% !important;
            max-width: 800px;
            margin: 0 auto;
        }
        
        .select2-selection--multiple {
            min-height: 50px !important;
            border: 2px solid #ddd !important;
            border-radius: 8px !important;
            padding: 5px !important;
        }
        
        .select2-selection--multiple:focus-within {
            border-color: #4CAF50 !important;
        }
        
        .select2-selection__choice {
            background-color: #4CAF50 !important;
            border: 1px solid #45a049 !important;
            border-radius: 15px !important;
            color: white !important;
            padding: 5px 10px !important;
            margin: 3px !important;
            font-size: 13px !important;
        }
        
        .select2-selection__choice__remove {
            color: white !important;
            margin-right: 8px !important;
        }
        
        .select2-selection__choice__remove:hover {
            color: #ffcccc !important;
        }
        
        .select2-dropdown {
            border: 2px solid #ddd !important;
            border-radius: 8px !important;
        }
        
        .select2-results__option {
            padding: 10px 15px !important;
            font-size: 14px !important;
            line-height: 1.4 !important;
            border-bottom: 1px solid #f0f0f0 !important;
        }
        
        .select2-results__option--highlighted {
            background-color: #f8f9fa !important;
            color: #333 !important;
        }
        
        .select2-results__option[aria-selected="true"] {
            background-color: #e8f5e8 !important;
            color: #2d5a2d !important;
        }
        
        .option-text {
            display: block;
        }
        
        .option-name {
            font-weight: 500;
            color: #333;
            margin-bottom: 3px;
        }
        
        .option-id {
            font-size: 12px;
            color: #666;
            font-family: monospace;
        }
        
        .selection-buttons {
            display: flex;
            justify-content: center;
            gap: 10px;
            margin-top: 15px;
        }
        
        .selection-buttons button {
            padding: 8px 16px;
            background-color: #3498db;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 0.9em;
        }
        
        .selection-buttons button:hover {
            background-color: #2980b9;
        }
        
        .selected-info {
            text-align: center;
            margin-top: 10px;
            color: #666;
            font-size: 14px;
        }

        .charts-section-wrapper {
            width: 95%;
            max-width: 1600px;
            margin: 30px auto;
        }
        .charts-row {
            /* 変更点: 横並びのレイアウトを解除し、ブロック要素として扱います */
            display: block; /* 以前は flex でした */
            /* justify-content: center; REMOVED */
            /* gap: 20px; REMOVED */
            margin-bottom: 20px;
        }
        .charts-row:last-child {
            margin-bottom: 0;
        }

        .chart-container {
            /* 変更点: 横幅を100%にし、flexの計算を削除 */
            /* flex: 1 1 calc(50% - 10px); REMOVED */
            width: 100%; /* 以前は flex 計算でした */
            min-width: 300px;
            height: 550px; 
            background-color: transparent;
            border-radius: 8px;
            box-shadow: none;
            padding: 20px;
            box-sizing: border-box;
            position: relative;
            /* グラフ間の縦方向のスペースを確保 */
            margin-bottom: 20px; /* 各グラフの下に余白を追加 */
        }
        /* このクラスは、全グラフが100%幅になるため、実質的に不要になりますが、残しておいても問題ありません */
        .chart-container.full-width-chart {
            flex: 1 1 100%;
            max-width: calc(100% - 20px);
        }
        .chart-container h2 {
            position: absolute;
            top: 10px;
            left: 0;
            right: 0;
            text-align: center;
            font-size: 1.2em;
            color: #333;
            z-index: 10;
            margin: 0;
        }
        .plotly-graph-div {
            width: 100% !important;
            height: 100% !important;
            position: absolute;
            top: 0;
            left: 0;
            padding-top: 40px;
            box-sizing: border-box;
        }

        .loading {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(255, 255, 255, 0.9);
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.15);
            z-index: 1000;
            display: none;
        }
    </style>

    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <link href="https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/css/select2.min.css" rel="stylesheet" />
    <script src="https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/js/select2.min.js"></script>
    
    <script src="https://cdn.sheetjs.com/xlsx-0.20.1/package/dist/xlsx.full.min.js"></script>
    <script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
</head>
<body>
    <h1>ふるさと納税 返礼品 各指標 時系列分析レポート - 自治体フィルタ対応版</h1>

    <div class="loading" id="loadingIndicator">
        <p>データを処理中...</p>
    </div>

    <div class="input-section">
        <label for="folderInput">フォルダを選択 (複数自治体のフォルダを含む):</label>
        <input type="file" id="folderInput" webkitdirectory multiple>
        <button id="loadDataButton">データを読み込む</button>
        <div id="fileLoadStatus"></div>
        <div id="cityInfo" class="city-info" style="display: none;"></div>
    </div>

    <div class="select-container">
        <div class="analysis-target-container">
            <label for="analysisTargetSelector">分析対象:</label>
            <select id="analysisTargetSelector" onchange="handleAnalysisTargetChange()">
                <option value="individual">個別商品分析</option>
                <option value="keyword">キーワード集計</option>
            </select>
        </div>
        
        <div class="comparison-container">
            <label for="comparisonMode">比較設定:</label>
            <select id="comparisonMode" onchange="handleComparisonModeChange()">
                <option value="none">比較なし</option>
                <option value="vs_total">選択自治体 vs 全体平均</option>
                <option value="vs_cities">複数自治体比較</option>
            </select>
            <select id="targetCitySelector" style="display: none;" onchange="debouncedFilterAndDisplay()">
                <option value="">自治体を選択</option>
            </select>
            <div id="multiCitySelector" style="display: none;">
                <label for="cityMultiSelect">🏙️ 比較する自治体を選択 (複数選択可):</label>
                <select id="cityMultiSelect" multiple="multiple">
                    </select>
                <div class="selection-buttons">
                    <button onclick="selectAllCities()">全選択</button>
                    <button onclick="clearAllCities()">全解除</button>
                </div>
                <div class="selected-info" id="selectedCitiesInfo">
                    選択中: 0自治体
                </div>
            </div>
        </div>

        <div class="city-filter-container">
            <div id="cityFilterSelector" style="display: none;">
                <label for="cityFilterMultiSelect">🏙️ 対象自治体を選択 (複数選択可、未選択時は全自治体):</label>
                <select id="cityFilterMultiSelect" multiple="multiple">
                    </select>
                <div class="selection-buttons">
                    <button onclick="selectAllCityFilters()">全選択</button>
                    <button onclick="clearAllCityFilters()">全解除</button>
                </div>
                <div class="selected-info" id="selectedCityFiltersInfo">
                    選択中: 0自治体 (全自治体を対象)
                </div>
            </div>
        </div>
        
        <div>
            <label for="displayModeSelector">表示モード:</label>
            <select id="displayModeSelector" onchange="handleDisplayModeChange()">
                <option value="timeseries">時系列分析</option>
                <option value="annual_comparison">年度比較</option>
            </select>
        </div>
        <div>
            <label for="yearSelector">年度選択:</label>
            <select id="yearSelector" onchange="debouncedFilterAndDisplay()">
                <option value="all" selected>全年度</option>
                <option value="2023">2023年度</option>
                <option value="2024">2024年度</option>
                <option value="2025">2025年度</option>
            </select>
        </div>
        <div>
            <label for="minSalesQuantitySelector">最小売上個数:</label>
            <select id="minSalesQuantitySelector" onchange="debouncedFilterAndDisplay()">
                <option value="0">0個以上</option>
                <option value="1">1個以上</option>
                <option value="10">10個以上</option>
                <option value="50">50個以上</option>
                <option value="100">100個以上</option>
                <option value="500">500個以上</option>
            </select>
        </div>
        
        <div class="advanced-search-container">
            <h3>🔍 高度な検索条件</h3>
            
            <div class="search-row">
                <label for="productNameSearch">商品名:</label>
                <input type="text" id="productNameSearch" placeholder="キーワードをカンマ区切りで入力（例：牛肉,豚肉,鶏肉）" oninput="debouncedFilterAndDisplay()">
                <select id="productNameCondition" onchange="debouncedFilterAndDisplay()">
                    <option value="OR">OR</option>
                    <option value="AND">AND</option>
                </select>
            </div>
            
            <div class="search-row">
                <label for="genreSearch">ジャンル:</label>
                <input type="text" id="genreSearch" placeholder="ジャンルをカンマ区切りで入力（例：肉類,海鮮,野菜）" oninput="debouncedFilterAndDisplay()">
                <select id="genreCondition" onchange="debouncedFilterAndDisplay()">
                    <option value="OR">OR</option>
                    <option value="AND">AND</option>
                </select>
            </div>
            
            <div class="search-condition-row">
                <label>商品名とジャンルの結合:</label>
                <select id="overallCondition" onchange="debouncedFilterAndDisplay()">
                    <option value="AND">AND</option>
                    <option value="OR">OR</option>
                </select>
            </div>
            
            <div class="search-buttons">
                <button class="btn-secondary" onclick="clearAllSearchConditions()">条件をクリア</button>
            </div>
            
            <div class="search-stats" id="searchStats">
                検索条件を入力すると該当商品数が表示されます
            </div>
        </div>
        
        <div id="yearComparisonSection" class="year-comparison-section" style="display: none;">
            <h3>🔍 年度比較設定</h3>
            <p>比較したい年度を選択してください（複数選択可）</p>
            <div class="year-selector-grid">
                <div class="year-checkbox">
                    <input type="checkbox" id="year2023" value="2023" checked onchange="debouncedFilterAndDisplay()">
                    <label for="year2023">2023年度</label>
                </div>
                <div class="year-checkbox">
                    <input type="checkbox" id="year2024" value="2024" checked onchange="debouncedFilterAndDisplay()">
                    <label for="year2024">2024年度</label>
                </div>
                <div class="year-checkbox">
                    <input type="checkbox" id="year2025" value="2025" checked onchange="debouncedFilterAndDisplay()">
                    <label for="year2025">2025年度</label>
                </div>
            </div>
        </div>
        
        <div id="itemSelectorSection" class="item-selector-container">
            <label for="itemSelector">商品を選択 (複数選択可):</label>
            <select id="itemSelector" multiple="multiple">
                </select>
            <div class="selection-buttons">
                <button onclick="selectAllItems()">全選択</button>
                <button onclick="clearAllItems()">全解除</button>
                <button onclick="toggleNameLength()">名前表示切替</button>
            </div>
            <div class="selected-info" id="selectedInfo">
                選択中: 0件 | 検索機能で絞り込みできます
            </div>
        </div>
        
        <div id="keywordInputSection" class="keyword-input-container" style="display: none;">
            <label for="keywordInput">キーワードを入力:</label>
            <input type="text" id="keywordInput" oninput="handleKeywordInput()" placeholder="例：肉、パン、海鮮...">
            <div class="keyword-stats" id="keywordStats">
                キーワードを入力すると該当商品数が表示されます
            </div>
        </div>
    </div>

    <div class="charts-section-wrapper">
        <div class="charts-row">
            <div class="chart-container" id="chart_アクセス人数"><h2>アクセス人数</h2></div>
            <div class="chart-container" id="chart_売上件数"><h2>売上件数</h2></div>
        </div>
        <div class="charts-row">
            <div class="chart-container" id="chart_転換率"><h2>転換率</h2></div>
            <div class="chart-container" id="chart_お気に入り登録ユーザ数"><h2>お気に入り登録ユーザ数</h2></div>
        </div>
        <div class="charts-row">
            <div class="chart-container" id="chart_売上"><h2>売上</h2></div>
            <div class="chart-container" id="chart_返礼品数"><h2>返礼品数</h2></div>
        </div>
    </div>

    <script>
        // 定数定義
        const ITEM_NAME_COLUMN = '商品ID';
        const DISPLAY_PRODUCT_NAME_COLUMN = '商品名';
        const GENRE_COLUMN_NAME_IN_EXCEL = 'ジャンル';
        const METRICS_TO_PLOT = [
            'アクセス人数',
            'お気に入り登録ユーザ数',
            '売上',
            '売上件数',
            '転換率',
            '返礼品数'
        ];

        // カラーパレット
        const COLOR_PALETTE = [
            '#1f77b4', '#ff7f0e', '#2ca02c', '#d62728', '#9467bd',
            '#8c564b', '#e377c2', '#7f7f7f', '#bcbd22', '#17becf',
            '#aec7e8', '#ffbb78', '#98df8a', '#ff9896', '#c5b0d5',
            '#c49c94', '#f7b6d3', '#c7c7c7', '#dbdb8d', '#9edae5'
        ];

        // グローバル変数
        let aggregatedMonthlyData = [];
        let processedDataForCurrentView = {};
        let showFullNames = false;
        let currentKeyword = '';
        let loadedCities = [];
        let allCityData = {};
        let isProcessing = false;

        // DOM要素
        const folderInput = document.getElementById('folderInput');
        const loadDataButton = document.getElementById('loadDataButton');
        const fileLoadStatus = document.getElementById('fileLoadStatus');
        const cityInfo = document.getElementById('cityInfo');
        const analysisTargetSelector = document.getElementById('analysisTargetSelector');
        const comparisonMode = document.getElementById('comparisonMode');
        const targetCitySelector = document.getElementById('targetCitySelector');
        const displayModeSelector = document.getElementById('displayModeSelector');
        const yearSelector = document.getElementById('yearSelector');
        const yearComparisonSection = document.getElementById('yearComparisonSection');
        const minSalesQuantitySelector = document.getElementById('minSalesQuantitySelector');
        const itemSelectorSection = document.getElementById('itemSelectorSection');
        const keywordInputSection = document.getElementById('keywordInputSection');
        const keywordInput = document.getElementById('keywordInput');
        const keywordStats = document.getElementById('keywordStats');
        const loadingIndicator = document.getElementById('loadingIndicator');

        // 高度な検索機能の要素
        const productNameSearch = document.getElementById('productNameSearch');
        const productNameCondition = document.getElementById('productNameCondition');
        const genreSearch = document.getElementById('genreSearch');
        const genreCondition = document.getElementById('genreCondition');
        const overallCondition = document.getElementById('overallCondition');
        const searchStats = document.getElementById('searchStats');

        // デバウンス関数
        function debounce(func, wait) {
            let timeout;
            return function executedFunction(...args) {
                const later = () => {
                    clearTimeout(timeout);
                    func(...args);
                };
                clearTimeout(timeout);
                timeout = setTimeout(later, wait);
            };
        }

        // デバウンス付きフィルター更新
        const debouncedFilterAndDisplay = debounce(filterAndDisplayCharts, 500);

        // ローディング表示制御
        function showLoading() {
            if (!isProcessing) {
                isProcessing = true;
                loadingIndicator.style.display = 'block';
            }
        }

        function hideLoading() {
            isProcessing = false;
            loadingIndicator.style.display = 'none';
        }

        // ファイルパスから自治体名を抽出
        function extractCityName(filePath) {
            const pathParts = filePath.split('/');
            if (pathParts.length >= 2) {
                return pathParts[pathParts.length - 2]; // フォルダ名（自治体名）
            }
            return 'unknown';
        }

        // 分析対象変更処理
        function handleAnalysisTargetChange() {
            const target = analysisTargetSelector.value;
            
            if (target === 'individual') {
                // 個別商品分析モード
                itemSelectorSection.style.display = 'block';
                keywordInputSection.style.display = 'none';
            } else if (target === 'keyword') {
                // キーワード集計モード
                itemSelectorSection.style.display = 'none';
                keywordInputSection.style.display = 'block';
            }
            
            filterAndDisplayCharts();
        }

        // 比較モード変更処理
        function handleComparisonModeChange() {
            const mode = comparisonMode.value;
            
            if (mode === 'vs_total') {
                targetCitySelector.style.display = 'block';
                document.getElementById('multiCitySelector').style.display = 'none';
                document.getElementById('cityFilterSelector').style.display = 'none';
            } else if (mode === 'vs_cities') {
                targetCitySelector.style.display = 'none';
                document.getElementById('multiCitySelector').style.display = 'block';
                document.getElementById('cityFilterSelector').style.display = 'none';
                initializeCityMultiSelect();
            } else {
                // 比較なし
                targetCitySelector.style.display = 'none';
                document.getElementById('multiCitySelector').style.display = 'none';
                document.getElementById('cityFilterSelector').style.display = 'block';
                initializeCityFilterMultiSelect();
            }
            
            // キーワードが入力されている場合は統計を更新
            if (currentKeyword) {
                handleKeywordInput();
            }
            
            filterAndDisplayCharts();
        }

        // 表示モード変更処理
        function handleDisplayModeChange() {
            const mode = displayModeSelector.value;
            
            if (mode === 'annual_comparison') {
                // 年度比較モード
                yearComparisonSection.style.display = 'block';
                yearSelector.style.display = 'none';
                yearSelector.parentElement.style.display = 'none';
            } else {
                // 時系列分析モード
                yearComparisonSection.style.display = 'none';
                yearSelector.style.display = 'block';
                yearSelector.parentElement.style.display = 'flex';
            }
            
            // キーワードが入力されている場合は統計を更新
            if (currentKeyword) {
                handleKeywordInput();
            }
            
            filterAndDisplayCharts();
        }

        // 複数自治体選択の初期化
        function initializeCityMultiSelect() {
            $('#cityMultiSelect').select2({
                placeholder: "比較する自治体を選択してください",
                allowClear: true,
                width: '100%',
                closeOnSelect: false
            }).on('change', function() {
                updateSelectedCitiesInfo();
                debouncedFilterAndDisplay();
            });
        }

        // 自治体フィルタの初期化
        function initializeCityFilterMultiSelect() {
            $('#cityFilterMultiSelect').select2({
                placeholder: "対象自治体を選択してください (未選択時は全自治体)",
                allowClear: true,
                width: '100%',
                closeOnSelect: false
            }).on('change', function() {
                updateSelectedCityFiltersInfo();
                debouncedFilterAndDisplay();
            });
        }

        // 複数自治体選択オプションを更新
        function updateCityMultiSelector() {
            const currentSelected = $('#cityMultiSelect').val() || [];
            
            $('#cityMultiSelect').empty();
            
            loadedCities.forEach(city => {
                const option = $('<option></option>')
                    .attr('value', city)
                    .text(city);
                
                if (currentSelected.includes(city)) {
                    option.prop('selected', true);
                }
                
                $('#cityMultiSelect').append(option);
            });
            
            $('#cityMultiSelect').trigger('change');
        }

        // 自治体フィルタオプションを更新
        function updateCityFilterMultiSelector() {
            const currentSelected = $('#cityFilterMultiSelect').val() || [];
            
            $('#cityFilterMultiSelect').empty();
            
            loadedCities.forEach(city => {
                const option = $('<option></option>')
                    .attr('value', city)
                    .text(city);
                
                if (currentSelected.includes(city)) {
                    option.prop('selected', true);
                }
                
                $('#cityFilterMultiSelect').append(option);
            });
            
            $('#cityFilterMultiSelect').trigger('change');
        }

        // 選択された自治体の情報を更新
        function updateSelectedCitiesInfo() {
            const selectedCount = $('#cityMultiSelect').val() ? $('#cityMultiSelect').val().length : 0;
            document.getElementById('selectedCitiesInfo').textContent = `選択中: ${selectedCount}自治体`;
            
            // キーワードが入力されている場合は統計を更新
            if (currentKeyword) {
                handleKeywordInput();
            }
        }

        // 選択された自治体フィルタの情報を更新
        function updateSelectedCityFiltersInfo() {
            const selectedCount = $('#cityFilterMultiSelect').val() ? $('#cityFilterMultiSelect').val().length : 0;
            const infoText = selectedCount > 0 ? `選択中: ${selectedCount}自治体` : '選択中: 0自治体 (全自治体を対象)';
            document.getElementById('selectedCityFiltersInfo').textContent = infoText;
            
            // キーワードが入力されている場合は統計を更新
            if (currentKeyword) {
                handleKeywordInput();
            }
        }

        // 全自治体選択
        function selectAllCities() {
            $('#cityMultiSelect option').prop('selected', true);
            $('#cityMultiSelect').trigger('change');
        }

        // 全自治体解除
        function clearAllCities() {
            $('#cityMultiSelect option').prop('selected', false);
            $('#cityMultiSelect').trigger('change');
        }

        // 全自治体フィルタ選択
        function selectAllCityFilters() {
            $('#cityFilterMultiSelect option').prop('selected', true);
            $('#cityFilterMultiSelect').trigger('change');
        }

        // 全自治体フィルタ解除
        function clearAllCityFilters() {
            $('#cityFilterMultiSelect option').prop('selected', false);
            $('#cityFilterMultiSelect').trigger('change');
        }

        // 選択された自治体リストを取得
        function getSelectedCities() {
            return $('#cityMultiSelect').val() || [];
        }

        // 選択された自治体フィルタリストを取得
        function getSelectedCityFilters() {
            return $('#cityFilterMultiSelect').val() || [];
        }

        // 高度な検索条件をクリア
        function clearAllSearchConditions() {
            productNameSearch.value = '';
            genreSearch.value = '';
            productNameCondition.value = 'OR';
            genreCondition.value = 'OR';
            overallCondition.value = 'AND';
            searchStats.textContent = '検索条件を入力すると該当商品数が表示されます';
            filterAndDisplayCharts();
        }

        // 高度な検索条件に基づいて商品をフィルタ
        function matchesAdvancedSearch(row) {
            const productNameText = productNameSearch.value.toLowerCase().trim();
            const genreText = genreSearch.value.toLowerCase().trim();
            
            if (!productNameText && !genreText) {
                return true; // 検索条件なしの場合は全てマッチ
            }
            
            const productName = (row[DISPLAY_PRODUCT_NAME_COLUMN] || '').toLowerCase();
            const genre = (row[GENRE_COLUMN_NAME_IN_EXCEL] || '').toLowerCase();
            
            let productNameMatch = true;
            let genreMatch = true;
            
            // 商品名の検索
            if (productNameText) {
                const productNameKeywords = productNameText.split(',').map(k => k.trim()).filter(k => k);
                if (productNameKeywords.length > 0) {
                    if (productNameCondition.value === 'AND') {
                        productNameMatch = productNameKeywords.every(keyword => productName.includes(keyword));
                    } else { // OR
                        productNameMatch = productNameKeywords.some(keyword => productName.includes(keyword));
                    }
                }
            }
            
            // ジャンルの検索
            if (genreText) {
                const genreKeywords = genreText.split(',').map(k => k.trim()).filter(k => k);
                if (genreKeywords.length > 0) {
                    if (genreCondition.value === 'AND') {
                        genreMatch = genreKeywords.every(keyword => genre.includes(keyword));
                    } else { // OR
                        genreMatch = genreKeywords.some(keyword => genre.includes(keyword));
                    }
                }
            }
            
            // 商品名とジャンルの結合
            if (productNameText && genreText) {
                if (overallCondition.value === 'AND') {
                    return productNameMatch && genreMatch;
                } else { // OR
                    return productNameMatch || genreMatch;
                }
            } else if (productNameText) {
                return productNameMatch;
            } else if (genreText) {
                return genreMatch;
            }
            
            return true;
        }

        // 検索統計を更新
        function updateSearchStats() {
            const selectedYears = getSelectedYears();
            const minSalesQuantity = parseInt(minSalesQuantitySelector.value);
            
            let matchCount = 0;
            let totalCount = 0;
            
            const uniqueItems = new Set();
            
            aggregatedMonthlyData.forEach(row => {
                // 年度フィルタ
                const displayMode = displayModeSelector.value;
                if (displayMode === 'timeseries') {
                    const yearSelectorValue = yearSelector.value;
                    if (yearSelectorValue !== 'all' && row.FiscalYear != parseInt(yearSelectorValue)) {
                        return;
                    }
                } else if (displayMode === 'annual_comparison') {
                    if (selectedYears.length > 0 && !selectedYears.includes(row.FiscalYear.toString())) {
                        return;
                    }
                }
                
                // 売上個数フィルタ
                const salesQuantity = row['売上件数'] || 0;
                if (salesQuantity < minSalesQuantity) {
                    return;
                }
                
                // 自治体フィルタ（比較なしの場合）
                if (comparisonMode.value === 'none') {
                    const selectedCityFilters = getSelectedCityFilters();
                    if (selectedCityFilters.length > 0 && !selectedCityFilters.includes(row.cityName)) {
                        return;
                    }
                }
                
                const itemKey = row[ITEM_NAME_COLUMN];
                if (uniqueItems.has(itemKey)) {
                    return;
                }
                
                uniqueItems.add(itemKey);
                totalCount++;
                
                if (matchesAdvancedSearch(row)) {
                    matchCount++;
                }
            });
            
            const productNameText = productNameSearch.value.trim();
            const genreText = genreSearch.value.trim();
            
            if (productNameText || genreText) {
                let conditionText = '';
                if (productNameText && genreText) {
                    conditionText = `商品名「${productNameText}」${overallCondition.value === 'AND' ? 'かつ' : 'または'}ジャンル「${genreText}」`;
                } else if (productNameText) {
                    conditionText = `商品名「${productNameText}」`;
                } else if (genreText) {
                    conditionText = `ジャンル「${genreText}」`;
                }
                
                searchStats.innerHTML = `
                    <strong>${conditionText}</strong>の検索結果:<br>
                    該当商品数: <strong>${matchCount}</strong>件 / 全体: ${totalCount}件
                `;
            } else {
                searchStats.textContent = '検索条件を入力すると該当商品数が表示されます';
            }
        }

        // キーワード入力処理
        function handleKeywordInput() {
            currentKeyword = keywordInput.value.toLowerCase().trim();
            
            if (currentKeyword === '') {
                keywordStats.textContent = 'キーワードを入力すると該当商品数が表示されます';
                clearCharts();
                return;
            }
            
            // 比較モードと選択年度を取得
            const comparisonTarget = comparisonMode.value;
            const targetCity = targetCitySelector.value;
            const selectedCities = getSelectedCities();
            const selectedCityFilters = getSelectedCityFilters();
            const selectedYears = getSelectedYears();
            const displayMode = displayModeSelector.value;
            
            // 年度情報の表示文字列を作成
            let yearText = '';
            if (displayMode === 'timeseries') {
                const yearSelectorValue = yearSelector.value;
                if (yearSelectorValue === 'all') {
                    yearText = '（全年度）';
                } else {
                    yearText = `（${yearSelectorValue}年度）`;
                }
            } else if (displayMode === 'annual_comparison') {
                if (selectedYears.length > 0) {
                    yearText = `（${selectedYears.join('・')}年度）`;
                } else {
                    yearText = '（年度未選択）';
                }
            }
            
            let statsHtml = `キーワード「<strong>${keywordInput.value}</strong>」の該当商品数${yearText}:<br>`;
            
            if (comparisonTarget === 'vs_total' && targetCity) {
                // 選択自治体 vs 全体平均の場合
                const cityProducts = getMatchingProductsByKeywordAndCity(currentKeyword, targetCity, selectedYears);
                const totalProducts = getMatchingProductsByKeyword(currentKeyword, selectedYears);
                
                statsHtml += `
                    <div style="margin: 5px 0;">
                        📍 <strong>${targetCity}</strong>: ${cityProducts.length}件
                    </div>
                    <div style="margin: 5px 0;">
                        🌍 <strong>全体</strong>: ${totalProducts.length}件 (${loadedCities.length}自治体合計)
                    </div>
                `;
            } else if (comparisonTarget === 'vs_cities' && selectedCities.length > 0) {
                // 複数自治体比較の場合
                selectedCities.forEach(cityName => {
                    const cityProducts = getMatchingProductsByKeywordAndCity(currentKeyword, cityName, selectedYears);
                    statsHtml += `
                        <div style="margin: 5px 0;">
                            🏙️ <strong>${cityName}</strong>: ${cityProducts.length}件
                        </div>
                    `;
                });
                
                // 全体も表示
                const totalProducts = getMatchingProductsByKeyword(currentKeyword, selectedYears);
                statsHtml += `
                    <div style="margin: 5px 0; padding-top: 5px; border-top: 1px solid #ddd;">
                        🌍 <strong>全体合計</strong>: ${totalProducts.length}件 (${loadedCities.length}自治体)
                    </div>
                `;
            } else {
                // 比較なしの場合（自治体フィルタ対応）
                const totalProducts = getMatchingProductsByKeyword(currentKeyword, selectedYears, selectedCityFilters);
                const targetCityCount = selectedCityFilters.length > 0 ? selectedCityFilters.length : loadedCities.length;
                const targetCityText = selectedCityFilters.length > 0 ? `${selectedCityFilters.join('・')}` : '全自治体';
                
                statsHtml += `
                    <div style="margin: 5px 0;">
                        🌍 <strong>${targetCityText}</strong>: ${totalProducts.length}件 (${targetCityCount}自治体${selectedCityFilters.length > 0 ? '' : '合計'})
                    </div>
                `;
            }
            
            if (getMatchingProductsByKeyword(currentKeyword, selectedYears, selectedCityFilters).length > 0) {
                statsHtml += '<br><small>下のグラフに集計結果が表示されます</small>';
            }
            
            keywordStats.innerHTML = statsHtml;
            
            debouncedFilterAndDisplay();
        }

        // キーワードに該当する商品を取得（自治体フィルタ対応版）
        function getMatchingProductsByKeyword(keyword, selectedYears = null, selectedCityFilters = []) {
            const uniqueProducts = new Set();
            aggregatedMonthlyData.forEach(row => {
                // 年度フィルタ
                if (selectedYears && selectedYears.length > 0 && !selectedYears.includes(row.FiscalYear.toString())) {
                    return;
                }
                
                // 自治体フィルタ
                if (selectedCityFilters.length > 0 && !selectedCityFilters.includes(row.cityName)) {
                    return;
                }
                
                const productName = (row[DISPLAY_PRODUCT_NAME_COLUMN] || '').toLowerCase();
                const genre = (row[GENRE_COLUMN_NAME_IN_EXCEL] || '').toLowerCase();
                
                if (productName.includes(keyword) || genre.includes(keyword)) {
                    uniqueProducts.add(row[ITEM_NAME_COLUMN]);
                }
            });
            return Array.from(uniqueProducts);
        }

        // 特定自治体のキーワード該当商品を取得
        function getMatchingProductsByKeywordAndCity(keyword, cityName, selectedYears = null) {
            const uniqueProducts = new Set();
            aggregatedMonthlyData.forEach(row => {
                if (row.cityName !== cityName) return;
                
                // 年度フィルタ
                if (selectedYears && selectedYears.length > 0 && !selectedYears.includes(row.FiscalYear.toString())) {
                    return;
                }
                
                const productName = (row[DISPLAY_PRODUCT_NAME_COLUMN] || '').toLowerCase();
                const genre = (row[GENRE_COLUMN_NAME_IN_EXCEL] || '').toLowerCase();
                
                if (productName.includes(keyword) || genre.includes(keyword)) {
                    uniqueProducts.add(row.originalItemId);
                }
            });
            return Array.from(uniqueProducts);
        }

        // グラフをクリア
        function clearCharts() {
            METRICS_TO_PLOT.forEach(metric => {
                const chartDiv = document.getElementById('chart_' + metric);
                Plotly.purge(chartDiv.id);
                chartDiv.innerHTML = `<h2>${metric}</h2><p style="text-align:center; color:#888;">データを選択してください。</p>`;
            });
        }

        // フォルダ読み込み処理
        async function handleFolderLoad() {
            if (isProcessing) return;
            
            showLoading();
            
            const files = folderInput.files;
            if (files.length === 0) {
                fileLoadStatus.textContent = 'フォルダを選択してください。';
                fileLoadStatus.style.color = 'orange';
                hideLoading();
                return;
            }

            fileLoadStatus.textContent = 'フォルダを読み込み中... (0%)';
            fileLoadStatus.style.color = 'blue';
            
            aggregatedMonthlyData = [];
            allCityData = {};
            loadedCities = [];

            // ファイルを自治体別に分類
            const filesByCity = {};
            Array.from(files).forEach(file => {
                if (file.name.endsWith('.xlsx') || file.name.endsWith('.xls')) {
                    const cityName = extractCityName(file.webkitRelativePath);
                    if (!filesByCity[cityName]) {
                        filesByCity[cityName] = [];
                    }
                    filesByCity[cityName].push(file);
                }
            });

            loadedCities = Object.keys(filesByCity);
            let processedCities = 0;

            try {
                for (const cityName of loadedCities) {
                    const cityFiles = filesByCity[cityName];
                    allCityData[cityName] = [];

                    for (const file of cityFiles) {
                        try {
                            const data = await readFileAsync(file);
                            const workbook = XLSX.read(data, { type: 'binary' });
                            const sheetName = workbook.SheetNames[0];
                            const worksheet = workbook.Sheets[sheetName];

                            const jsonDataRaw = XLSX.utils.sheet_to_json(worksheet, { header: 1, range: 5, raw: false });
                            
                            if (jsonDataRaw.length === 0) {
                                console.warn(`ファイル ${file.name} には有効なデータ行がありませんでした。`);
                                continue;
                            }

                            const headers = jsonDataRaw[0];
                            const dataRows = jsonDataRaw.slice(1);

                            const fileDateStr = getMonthYearFromFile(file.name);
                            if (!fileDateStr) {
                                console.warn(`ファイル名から日付を解析できませんでした: ${file.name}`);
                                continue;
                            }
                            const fileDate = new Date(fileDateStr);
                            const fiscalYear = getFiscalYear(fileDate);

                            const rawRecordsInFile = dataRows.map(rowArray => {
                                let record = {};
                                headers.forEach((header, index) => {
                                    record[header] = rowArray[index];
                                });
                                record.Date = fileDate;
                                record.FiscalYear = fiscalYear;
                                return record;
                            });
                            
                            const aggregatedDataForThisFile = aggregateRawDataToMonthly(rawRecordsInFile, cityName);
                            allCityData[cityName].push(...aggregatedDataForThisFile);

                        } catch (error) {
                            console.error(`ファイル ${file.name} の読み込み中にエラー:`, error);
                        }
                    }

                    processedCities++;
                    const percentage = Math.round((processedCities / loadedCities.length) * 100);
                    fileLoadStatus.textContent = `フォルダを読み込み中... (${percentage}%)`;
                }

                // 全体データを統合
                Object.values(allCityData).forEach(cityData => {
                    aggregatedMonthlyData.push(...cityData);
                });

                // 自治体選択オプションを更新
                updateCitySelector();
                
                fileLoadStatus.textContent = `全${loadedCities.length}自治体のデータを正常に読み込みました。`;
                fileLoadStatus.style.color = 'green';
                
                filterAndDisplayCharts();

            } catch (error) {
                console.error('データ読み込みエラー:', error);
                fileLoadStatus.textContent = 'データ読み込みに失敗しました。';
                fileLoadStatus.style.color = 'red';
            }

            hideLoading();
        }

        // 自治体選択オプションを更新
        function updateCitySelector() {
            // 既存の単一選択用
            targetCitySelector.innerHTML = '<option value="">自治体を選択</option>';
            loadedCities.forEach(city => {
                const option = document.createElement('option');
                option.value = city;
                option.textContent = city;
                targetCitySelector.appendChild(option);
            });
            
            // 複数選択用も更新
            updateCityMultiSelector();
            updateCityFilterMultiSelector();
        }

        function readFileAsync(file) {
            return new Promise((resolve, reject) => {
                const reader = new FileReader();
                reader.onload = (e) => resolve(e.target.result);
                reader.onerror = (e) => reject(e);
                reader.readAsBinaryString(file);
            });
        }

        function getMonthYearFromFile(filename) {
            const match = filename.match(/(\d{8})_(\d{8})_item_list\.xlsx/);
            if (match && match[1]) {
                const dateStr = match[1];
                return `${dateStr.substring(0, 4)}-${dateStr.substring(4, 6)}-01`;
            }
            return null;
        }

        function getFiscalYear(date) {
            const year = date.getFullYear();
            const month = date.getMonth() + 1;
            return (month >= 4) ? year : year - 1;
        }

        // 生データを月次・商品IDごとに集計する関数
        function aggregateRawDataToMonthly(rawRecords, cityName) {
            const monthlyAggregated = {};

            rawRecords.forEach(row => {
                const itemId = row[ITEM_NAME_COLUMN];
                if (!itemId) return;

                // 複合キーを作成
                const uniqueItemId = `${cityName}_${itemId}`;
                const dateKey = row.Date.toISOString().slice(0, 7);
                const key = `${uniqueItemId}_${dateKey}`;
                
                if (!monthlyAggregated[key]) {
                    monthlyAggregated[key] = {
                        [ITEM_NAME_COLUMN]: uniqueItemId,
                        'originalItemId': itemId,
                        'cityName': cityName,
                        Date: row.Date,
                        FiscalYear: row.FiscalYear
                    };

                    if (row.hasOwnProperty(DISPLAY_PRODUCT_NAME_COLUMN)) {
                        monthlyAggregated[key][DISPLAY_PRODUCT_NAME_COLUMN] = row[DISPLAY_PRODUCT_NAME_COLUMN];
                        monthlyAggregated[key]['displayName'] = `[${cityName}] ${row[DISPLAY_PRODUCT_NAME_COLUMN]}`;
                    } else {
                        monthlyAggregated[key][DISPLAY_PRODUCT_NAME_COLUMN] = `ID: ${itemId}`;
                        monthlyAggregated[key]['displayName'] = `[${cityName}] ID: ${itemId}`;
                    }

                    if (GENRE_COLUMN_NAME_IN_EXCEL && row.hasOwnProperty(GENRE_COLUMN_NAME_IN_EXCEL)) {
                        monthlyAggregated[key][GENRE_COLUMN_NAME_IN_EXCEL] = row[GENRE_COLUMN_NAME_IN_EXCEL];
                    } else {
                        monthlyAggregated[key][GENRE_COLUMN_NAME_IN_EXCEL] = '';
                    }

                    METRICS_TO_PLOT.forEach(metric => {
                        if (metric === '返礼品数') {
                            monthlyAggregated[key][metric] = 0;
                        } else {
                            monthlyAggregated[key][metric] = 0;
                        }
                    });
                    // 転換率計算用
                    monthlyAggregated[key]['_sum_売上件数_for_転換率'] = 0;
                    monthlyAggregated[key]['_sum_アクセス人数_for_転換率'] = 0;
                }

                METRICS_TO_PLOT.forEach(metric => {
                    if (metric === '返礼品数') {
                        monthlyAggregated[key][metric] = 1; // 商品が存在する場合は1
                    } else {
                        const value = parseFloat(row[metric]) || 0;

                        if (metric === '転換率') {
                            // 転換率は元データではなく、売上件数/アクセス人数から計算
                            const salesQuantity = parseFloat(row['売上件数']) || 0;
                            const accessCount = parseFloat(row['アクセス人数']) || 0;

                            monthlyAggregated[key]['_sum_売上件数_for_転換率'] += salesQuantity;
                            monthlyAggregated[key]['_sum_アクセス人数_for_転換率'] += accessCount;
                        } else {
                            monthlyAggregated[key][metric] += value;
                        }
                    }
                });
            });

            // 転換率を最終計算
            Object.values(monthlyAggregated).forEach(item => {
                if (item['_sum_アクセス人数_for_転換率'] > 0) {
                    item['転換率'] = (item['_sum_売上件数_for_転換率'] / item['_sum_アクセス人数_for_転換率']) * 100;
                } else {
                    item['転換率'] = 0;
                }
                delete item['_sum_売上件数_for_転換率'];
                delete item['_sum_アクセス人数_for_転換率'];
            });
            
            return Object.values(monthlyAggregated);
        }

        // 商品名を整理する関数
        function cleanProductName(rawName) {
            if (!rawName) return '';
            
            let cleanedName = String(rawName);
            
            // 最初の【ふるさと納税】部分を除去
            cleanedName = cleanedName.replace(/^【ふるさと納税】/, '');

            // 最後の「/」「／」「|」以降を除去
            const separators = ['/', '／', '|'];
            let cutIndex = -1;

            separators.forEach(separator => {
                const index = cleanedName.lastIndexOf(separator);
                if (index !== -1) {
                    cutIndex = Math.max(cutIndex, index);
                }
            });

            if (cutIndex !== -1) {
                cleanedName = cleanedName.substring(0, cutIndex);
            }

            cleanedName = cleanedName.trim();
            return cleanedName;
        }

        // 表示用の短縮商品名を作成する関数
        function getDisplayName(cleanedName, maxLength = 60) {
            if (!cleanedName) return '';
            
            if (cleanedName.length <= maxLength) {
                return cleanedName;
            }
            
            return cleanedName.substring(0, maxLength) + '...';
        }

        // 色取得関数
        function getColorByIndex(index) {
            return COLOR_PALETTE[index % COLOR_PALETTE.length];
        }

        // Select2プルダウンを初期化
        function initializeSelect2() {
            $('#itemSelector').select2({
                placeholder: "商品を検索・選択してください",
                allowClear: true,
                width: '100%',
                closeOnSelect: false,
                templateResult: formatOption,
                templateSelection: formatSelection,
                escapeMarkup: function(markup) { return markup; }
            }).on('change', function() {
                updateSelectedInfo();
                debouncedUpdateChartsFromSelect();
            });
        }

        // デバウンス付きチャート更新
        const debouncedUpdateChartsFromSelect = debounce(updateChartsFromSelect, 300);

        // オプションの表示形式
        function formatOption(option) {
            if (!option.id) {
                return option.text;
            }
            
            const data = $(option.element).data();
            if (!data) {
                return option.text;
            }
            
            return $('<span class="option-text">' +
                '<div class="option-name">' + data.displayName + '</div>' +
                '<div class="option-id">ID: ' + data.originalId + '</div>' +
                '</span>');
        }

        // 選択されたアイテムの表示形式
        function formatSelection(option) {
            const data = $(option.element).data();
            return data ? data.displayName : option.text;
        }

        // プルダウンオプションを更新する関数
        function updateItemSelector() {
            const uniqueItemIds = Object.keys(processedDataForCurrentView).sort();
            
            // 現在の選択状態を保存
            const currentSelected = $('#itemSelector').val() || [];
            
            // オプションをクリア
            $('#itemSelector').empty();
            
            if (uniqueItemIds.length === 0) {
                $('#itemSelector').append('<option value="">該当する商品がありません</option>');
            } else {
                uniqueItemIds.forEach(itemId => {
                    const itemData = processedDataForCurrentView[itemId];
                    const displayName = showFullNames ? itemData.fullCleanedName : itemData.displayName;
                    
                    const option = $('<option></option>')
                        .attr('value', itemId)
                        .text(displayName)
                        .data({
                            displayName: displayName,
                            fullName: itemData.fullCleanedName,
                            itemId: itemId,
                            originalId: itemData.originalItemId
                        });
                    
                    // 以前の選択状態を復元
                    if (currentSelected.includes(itemId)) {
                        option.prop('selected', true);
                    }
                    
                    $('#itemSelector').append(option);
                });
            }
            
            // Select2を再初期化
            $('#itemSelector').trigger('change');
            updateSelectedInfo();
        }

        // 選択状態の情報を更新（スマート全選択対応）
        function updateSelectedInfo() {
            const selectedCount = $('#itemSelector').val() ? $('#itemSelector').val().length : 0;
            const totalCount = Object.keys(processedDataForCurrentView).length;
            $('#selectedInfo').text(`選択中: ${selectedCount}件 / 表示中: ${totalCount}件`);
        }

        // プルダウンからグラフを更新
        function updateChartsFromSelect() {
            const selectedItemIds = $('#itemSelector').val() || [];
            updateChartsMultiple(selectedItemIds);
        }

        // 全選択（表示中のアイテムのみ）
        function selectAllItems() {
            const visibleOptions = $('#itemSelector option').filter(function() {
                return this.value !== '';
            });
            visibleOptions.prop('selected', true);
            $('#itemSelector').trigger('change');
        }

        // 全解除
        function clearAllItems() {
            $('#itemSelector option').prop('selected', false);
            $('#itemSelector').trigger('change');
        }

        // 名前表示の切り替え
        function toggleNameLength() {
            showFullNames = !showFullNames;
            updateItemSelector();
        }

        // グラフ描画用にデータを整形する関数（時系列分析用）
        function processDataForTimeseries(data) {
            let processed = {};
            data.forEach(row => {
                const currentItemId = row[ITEM_NAME_COLUMN];
                if (!currentItemId) return;

                if (!processed[currentItemId]) {
                    // 商品名を整理
                    const rawProductName = row[DISPLAY_PRODUCT_NAME_COLUMN] || `ID: ${row.originalItemId}`;
                    const cleanedName = cleanProductName(rawProductName);
                    const displayName = row.displayName || getDisplayName(cleanedName);
                    
                    processed[currentItemId] = {
                        [ITEM_NAME_COLUMN]: currentItemId,
                        'originalItemId': row.originalItemId,
                        'cityName': row.cityName,
                        Date: [],
                        FiscalYear: row.FiscalYear,
                        displayName: displayName,
                        fullCleanedName: cleanedName,
                        rawName: rawProductName
                    };
                    METRICS_TO_PLOT.forEach(metric => {
                        processed[currentItemId][metric] = [];
                    });
                    if (GENRE_COLUMN_NAME_IN_EXCEL && row.hasOwnProperty(GENRE_COLUMN_NAME_IN_EXCEL)) {
                        processed[currentItemId][GENRE_COLUMN_NAME_IN_EXCEL] = row[GENRE_COLUMN_NAME_IN_EXCEL];
                    } else {
                        processed[currentItemId][GENRE_COLUMN_NAME_IN_EXCEL] = '';
                    }
                }
                processed[currentItemId].Date.push(new Date(row.Date));
                METRICS_TO_PLOT.forEach(metric => {
                    processed[currentItemId][metric].push(row[metric]);
                });
            });

            // 日付でソート
            Object.keys(processed).forEach(itemId => {
                const item = processed[itemId];
                const sortOrder = item.Date.map((date, index) => ({ date, index }));
                sortOrder.sort((a, b) => a.date.getTime() - b.date.getTime());

                const sortedDates = [];
                const sortedMetrics = {};
                METRICS_TO_PLOT.forEach(metric => sortedMetrics[metric] = []);

                sortOrder.forEach(orderItem => {
                    sortedDates.push(item.Date[orderItem.index]);
                    METRICS_TO_PLOT.forEach(metric => {
                        sortedMetrics[metric].push(item[metric][orderItem.index]);
                    });
                });
                item.Date = sortedDates;
                METRICS_TO_PLOT.forEach(metric => {
                    item[metric] = sortedMetrics[metric];
                });
            });

            return processed;
        }

        // キーワード集計用にデータを整形する関数（複数自治体比較対応版）
        function processDataForKeywordAnalysis(data, keyword, targetCity = null) {
            if (!keyword) return {};
            
            const monthlyAggregated = {};
            const cityCountPerMonth = {}; // 各月の参加自治体数をカウント
            
            data.forEach(row => {
                const productName = (row[DISPLAY_PRODUCT_NAME_COLUMN] || '').toLowerCase();
                const genre = (row[GENRE_COLUMN_NAME_IN_EXCEL] || '').toLowerCase();
                
                if (!productName.includes(keyword) && !genre.includes(keyword)) {
                    return;
                }
                
                const cityName = row.cityName;
                const dateKey = row.Date.toISOString().slice(0, 7);
                
                // 対象自治体のフィルタリング
                if (targetCity && cityName !== targetCity) {
                    return;
                }
                
                if (!monthlyAggregated[dateKey]) {
                    monthlyAggregated[dateKey] = {
                        Date: row.Date,
                        FiscalYear: row.FiscalYear
                    };
                    METRICS_TO_PLOT.forEach(metric => {
                        if (metric === '返礼品数') {
                            monthlyAggregated[dateKey][metric] = 0;
                        } else {
                            monthlyAggregated[dateKey][metric] = 0;
                        }
                    });
                    monthlyAggregated[dateKey]['_sum_売上件数_for_転換率'] = 0;
                    monthlyAggregated[dateKey]['_sum_アクセス人数_for_転換率'] = 0;
                    monthlyAggregated[dateKey]['_product_set'] = new Set();
                    cityCountPerMonth[dateKey] = new Set();
                }
                
                cityCountPerMonth[dateKey].add(cityName);
                monthlyAggregated[dateKey]['_product_set'].add(row[ITEM_NAME_COLUMN]);
                
                METRICS_TO_PLOT.forEach(metric => {
                    if (metric === '返礼品数') {
                        // 返礼品数は後で計算
                        return;
                    }
                    
                    const value = parseFloat(row[metric]) || 0;
                    if (metric === '転換率') {
                        monthlyAggregated[dateKey]['_sum_売上件数_for_転換率'] += (parseFloat(row['売上件数']) || 0);
                        monthlyAggregated[dateKey]['_sum_アクセス人数_for_転換率'] += (parseFloat(row['アクセス人数']) || 0);
                    } else {
                        monthlyAggregated[dateKey][metric] += value;
                    }
                });
            });
            
            // 転換率の最終計算と平均化処理
            Object.keys(monthlyAggregated).forEach(dateKey => {
                const monthData = monthlyAggregated[dateKey];
                const cityCount = cityCountPerMonth[dateKey].size;
                
                // 返礼品数を計算
                monthData['返礼品数'] = monthData['_product_set'].size;
                
                // 転換率計算
                if (monthData['_sum_アクセス人数_for_転換率'] > 0) {
                    monthData['転換率'] = (monthData['_sum_売上件数_for_転換率'] / monthData['_sum_アクセス人数_for_転換率']) * 100;
                } else {
                    monthData['転換率'] = 0;
                }
                
                // 全体の場合は平均化（単一自治体の場合はそのまま）
                if (!targetCity && cityCount > 1) {
                    // 転換率と返礼品数以外を平均化
                    METRICS_TO_PLOT.forEach(metric => {
                        if (metric !== '転換率' && metric !== '返礼品数') {
                            monthData[metric] = monthData[metric] / cityCount;
                        }
                    });
                }
                
                delete monthData['_sum_売上件数_for_転換率'];
                delete monthData['_sum_アクセス人数_for_転換率'];
                delete monthData['_product_set'];
            });
            
            // 時系列データ形式に変換
            const dates = [];
            const metrics = {};
            METRICS_TO_PLOT.forEach(metric => metrics[metric] = []);
            
            Object.keys(monthlyAggregated).sort().forEach(dateKey => {
                const monthData = monthlyAggregated[dateKey];
                dates.push(monthData.Date);
                METRICS_TO_PLOT.forEach(metric => {
                    metrics[metric].push(monthData[metric]);
                });
            });
            
            const displayName = targetCity ? `${targetCity}_${keyword}` : `全体平均_${keyword}`;
            
            return {
                [displayName]: {
                    Date: dates,
                    displayName: targetCity ? `[${targetCity}] キーワード「${keyword}」` : `[全体平均] キーワード「${keyword}」`,
                    ...metrics
                }
            };
        }

        // 複数自治体キーワード分析用関数
        function processMultipleCitiesKeywordAnalysis(data, keyword, selectedCities) {
            if (!keyword || selectedCities.length === 0) return {};
            
            const result = {};
            
            selectedCities.forEach(cityName => {
                const cityData = processDataForKeywordAnalysis(data, keyword, cityName);
                const cityKey = `${cityName}_${keyword}`;
                if (cityData[cityKey]) {
                    result[cityKey] = cityData[cityKey];
                }
            });
            
            return result;
        }

        // 年度比較用にデータを集計する関数
        function aggregateForAnnualComparison(data, itemId, selectedYears) {
            const annualData = {}; // fiscalMonth (0-11) -> year -> metricValue
            
            data.filter(row => row[ITEM_NAME_COLUMN] === itemId).forEach(row => {
                const date = row.Date;
                const calendarMonth = date.getMonth(); // 0-11 (1月=0, 12月=11)
                const fiscalYear = row.FiscalYear;

                // カレンダー月を会計年度月に変換（4月=0, 5月=1, ..., 3月=11）
                const fiscalMonth = calendarMonth >= 3 ? calendarMonth - 3 : calendarMonth + 9;

                // 選択された年度のみ処理
                if (!selectedYears.includes(fiscalYear.toString())) {
                    return;
                }

                if (!annualData[fiscalMonth]) {
                    annualData[fiscalMonth] = {};
                }
                if (!annualData[fiscalMonth][fiscalYear]) {
                    annualData[fiscalMonth][fiscalYear] = {};
                    METRICS_TO_PLOT.forEach(metric => {
                        annualData[fiscalMonth][fiscalYear][metric] = 0;
                    });
                    // 転換率計算用の一時的な集計値
                    annualData[fiscalMonth][fiscalYear]['_sum_売上件数_for_転換率'] = 0;
                    annualData[fiscalMonth][fiscalYear]['_sum_アクセス人数_for_転換率'] = 0;
                }
                
                METRICS_TO_PLOT.forEach(metric => {
                    if (metric === '返礼品数') {
                        annualData[fiscalMonth][fiscalYear][metric] = 1;
                    } else {
                        const value = parseFloat(row[metric]) || 0;
                        if (metric === '転換率') {
                            annualData[fiscalMonth][fiscalYear]['_sum_売上件数_for_転換率'] += (parseFloat(row['売上件数']) || 0);
                            annualData[fiscalMonth][fiscalYear]['_sum_アクセス人数_for_転換率'] += (parseFloat(row['アクセス人数']) || 0);
                        } else {
                            annualData[fiscalMonth][fiscalYear][metric] += value;
                        }
                    }
                });
            });

            // 転換率の最終計算
            Object.values(annualData).forEach(monthData => {
                Object.values(monthData).forEach(yearData => {
                    if (yearData['_sum_アクセス人数_for_転換率'] > 0) {
                        yearData['転換率'] = (yearData['_sum_売上件数_for_転換率'] / yearData['_sum_アクセス人数_for_転換率']) * 100;
                    } else {
                        yearData['転換率'] = 0;
                    }
                    delete yearData['_sum_売上件数_for_転換率'];
                    delete yearData['_sum_アクセス人数_for_転換率'];
                });
            });
            
            return annualData;
        }

        // キーワード年度比較用にデータを集計する関数（複数自治体対応版）
        function aggregateKeywordForAnnualComparison(data, keyword, selectedYears, targetCity = null) {
            const annualData = {}; // fiscalMonth (0-11) -> year -> metricValue
            const cityCountPerMonthYear = {}; // 各月年の自治体数カウント
            
            data.forEach(row => {
                const productName = (row[DISPLAY_PRODUCT_NAME_COLUMN] || '').toLowerCase();
                const genre = (row[GENRE_COLUMN_NAME_IN_EXCEL] || '').toLowerCase();
                
                // キーワードマッチング
                if (!productName.includes(keyword) && !genre.includes(keyword)) {
                    return;
                }
                
                const cityName = row.cityName;
                if (targetCity && cityName !== targetCity) {
                    return;
                }
                
                const date = row.Date;
                const calendarMonth = date.getMonth();
                const fiscalYear = row.FiscalYear;
                const fiscalMonth = calendarMonth >= 3 ? calendarMonth - 3 : calendarMonth + 9;

                if (!selectedYears.includes(fiscalYear.toString())) {
                    return;
                }

                if (!annualData[fiscalMonth]) {
                    annualData[fiscalMonth] = {};
                }
                if (!annualData[fiscalMonth][fiscalYear]) {
                    annualData[fiscalMonth][fiscalYear] = {};
                    METRICS_TO_PLOT.forEach(metric => {
                        if (metric === '返礼品数') {
                            annualData[fiscalMonth][fiscalYear][metric] = 0;
                        } else {
                            annualData[fiscalMonth][fiscalYear][metric] = 0;
                        }
                    });
                    annualData[fiscalMonth][fiscalYear]['_sum_売上件数_for_転換率'] = 0;
                    annualData[fiscalMonth][fiscalYear]['_sum_アクセス人数_for_転換率'] = 0;
                    annualData[fiscalMonth][fiscalYear]['_product_set'] = new Set();
                    cityCountPerMonthYear[`${fiscalMonth}_${fiscalYear}`] = new Set();
                }
                
                cityCountPerMonthYear[`${fiscalMonth}_${fiscalYear}`].add(cityName);
                annualData[fiscalMonth][fiscalYear]['_product_set'].add(row[ITEM_NAME_COLUMN]);
                
                METRICS_TO_PLOT.forEach(metric => {
                    if (metric === '返礼品数') {
                        // 返礼品数は後で計算
                        return;
                    }
                    
                    const value = parseFloat(row[metric]) || 0;
                    if (metric === '転換率') {
                        annualData[fiscalMonth][fiscalYear]['_sum_売上件数_for_転換率'] += (parseFloat(row['売上件数']) || 0);
                        annualData[fiscalMonth][fiscalYear]['_sum_アクセス人数_for_転換率'] += (parseFloat(row['アクセス人数']) || 0);
                    } else {
                        annualData[fiscalMonth][fiscalYear][metric] += value;
                    }
                });
            });

            // 転換率の最終計算と平均化処理
            Object.keys(annualData).forEach(fiscalMonth => {
                Object.keys(annualData[fiscalMonth]).forEach(fiscalYear => {
                    const yearData = annualData[fiscalMonth][fiscalYear];
                    const cityCount = cityCountPerMonthYear[`${fiscalMonth}_${fiscalYear}`] ? cityCountPerMonthYear[`${fiscalMonth}_${fiscalYear}`].size : 1;
                    
                    // 返礼品数を計算
                    yearData['返礼品数'] = yearData['_product_set'].size;
                    
                    if (yearData['_sum_アクセス人数_for_転換率'] > 0) {
                        yearData['転換率'] = (yearData['_sum_売上件数_for_転換率'] / yearData['_sum_アクセス人数_for_転換率']) * 100;
                    } else {
                        yearData['転換率'] = 0;
                    }
                    
                    // 全体の場合は平均化
                    if (!targetCity && cityCount > 1) {
                        METRICS_TO_PLOT.forEach(metric => {
                            if (metric !== '転換率' && metric !== '返礼品数') {
                                yearData[metric] = yearData[metric] / cityCount;
                            }
                        });
                    }
                    
                    delete yearData['_sum_売上件数_for_転換率'];
                    delete yearData['_sum_アクセス人数_for_転換率'];
                    delete yearData['_product_set'];
                });
            });
            
            return annualData;
        }

        // 複数自治体年度比較用関数
        function aggregateMultipleCitiesKeywordForAnnualComparison(data, keyword, selectedYears, selectedCities) {
            const result = {};
            
            selectedCities.forEach(cityName => {
                result[cityName] = aggregateKeywordForAnnualComparison(data, keyword, selectedYears, cityName);
            });
            
            return result;
        }

        // 選択された年度を取得する関数
        function getSelectedYears() {
            const mode = displayModeSelector.value;
            
            if (mode === 'annual_comparison') {
                // 年度比較モード：チェックボックスから取得
                const selectedYears = [];
                document.querySelectorAll('.year-checkbox input[type="checkbox"]:checked').forEach(checkbox => {
                    selectedYears.push(checkbox.value);
                });
                return selectedYears;
            } else {
                // 時系列分析モード：セレクトボックスから取得
                const selectedYear = yearSelector.value;
                if (selectedYear === 'all') {
                    return ['2023', '2024', '2025']; // 全年度
                } else {
                    return [selectedYear];
                }
            }
        }

        // ホバーフォーマットとY軸タイトルのヘルパー関数
        function getHoverFormat(metric) {
            if (metric === '転換率') return ':.2f';
            return ':,';
        }

        function getHoverSuffix(metric) {
            if (metric === '転換率') return '%';
            if (metric === '売上') return '円';
            if (metric === '売上件数') return '件';
            if (metric === 'アクセス人数' || metric === 'お気に入り登録ユーザ数') return '人';
            if (metric === '返礼品数') return '品';
            return '';
        }

        function getAxisTitle(metric) {
            if (metric === '転換率') return '転換率 (%)';
            if (metric === '売上') return '売上 (円)';
            if (metric === '売上件数') return '売上件数 (件)';
            if (metric === 'アクセス人数') return 'アクセス人数 (人)';
            if (metric === 'お気に入り登録ユーザ数') return 'お気に入り登録ユーザ数 (人)';
            if (metric === '返礼品数') return '返礼品数 (品)';
            return metric;
        }

        // グラフ描画関数（自治体フィルタ対応版）
        function plotChart(metric, selectedItemIds) {
            const analysisTarget = analysisTargetSelector.value;
            const displayMode = displayModeSelector.value;
            const comparisonTarget = comparisonMode.value;
            const targetCity = targetCitySelector.value;
            const selectedCities = getSelectedCities();
            const selectedCityFilters = getSelectedCityFilters();
            const chartDiv = document.getElementById('chart_' + metric);

            if (analysisTarget === 'individual' && (!selectedItemIds || selectedItemIds.length === 0)) {
                Plotly.purge(chartDiv.id);
                chartDiv.innerHTML = `<h2>${metric}</h2><p style="text-align:center; color:#888;">商品を選択してください。</p>`;
                return;
            }

            if (analysisTarget === 'keyword' && !currentKeyword) {
                Plotly.purge(chartDiv.id);
                chartDiv.innerHTML = `<h2>${metric}</h2><p style="text-align:center; color:#888;">キーワードを入力してください。</p>`;
                return;
            }

            let traces = [];
            let layout = {};

            if (analysisTarget === 'individual') {
                // 個別商品分析
                if (displayMode === 'timeseries') {
                    // 時系列分析モードの描画ロジック
                    traces = selectedItemIds.map((itemId, index) => {
                        const itemData = processedDataForCurrentView[itemId];
                        if (!itemData || !itemData[metric] || itemData[metric].length === 0) {
                            return null;
                        }
                        return {
                            x: itemData.Date,
                            y: itemData[metric],
                            mode: 'lines+markers',
                            name: itemData.displayName,
                            line: { 
                                shape: 'spline',
                                color: getColorByIndex(index),
                                width: 2
                            },
                            marker: {
                                color: getColorByIndex(index),
                                size: 6
                            },
                            hovertemplate: `${itemData.displayName}<br>%{x|%Y-%m-%d}<br>${metric}: %{y${getHoverFormat(metric)}}${getHoverSuffix(metric)}<extra></extra>`
                        };
                    }).filter(trace => trace !== null);

                } else if (displayMode === 'annual_comparison') {
                    // 年度比較モードの描画ロジック
                    const monthsInOrder = ["4月", "5月", "6月", "7月", "8月", "9月", "10月", "11月", "12月", "1月", "2月", "3月"];
                    const selectedYears = getSelectedYears();
                    
                    if (selectedYears.length === 0) {
                        Plotly.purge(chartDiv.id);
                        chartDiv.innerHTML = `<h2>${metric}</h2><p style="text-align:center; color:#888;">比較する年度を選択してください。</p>`;
                        return;
                    }
                    
                    selectedItemIds.forEach((itemId, itemIndex) => {
                        const itemAnnualData = aggregateForAnnualComparison(aggregatedMonthlyData, itemId, selectedYears);

                        let allFiscalYears = new Set();
                        for (let fiscalMonth = 0; fiscalMonth < 12; fiscalMonth++) {
                            if (itemAnnualData[fiscalMonth]) {
                                Object.keys(itemAnnualData[fiscalMonth]).forEach(year => {
                                    if (selectedYears.includes(year)) {
                                        allFiscalYears.add(year);
                                    }
                                });
                            }
                        }
                        const sortedFiscalYears = Array.from(allFiscalYears).sort();

                        sortedFiscalYears.forEach((fiscalYear, yearIndex) => {
                            const yValues = [];
                            const xValues = [];
                            
                            for (let fiscalMonth = 0; fiscalMonth < 12; fiscalMonth++) {
                                const monthData = itemAnnualData[fiscalMonth] && itemAnnualData[fiscalMonth][fiscalYear];
                                if (monthData && monthData[metric] !== undefined && monthData[metric] !== null) {
                                    yValues.push(monthData[metric]);
                                    xValues.push(monthsInOrder[fiscalMonth]);
                                }
                            }

                            if (yValues.length > 0) {
                                const productName = processedDataForCurrentView[itemId] ? processedDataForCurrentView[itemId].displayName : itemId;
                                const traceName = selectedItemIds.length > 1 ? 
                                    `${productName} ${fiscalYear}年度` : 
                                    `${fiscalYear}年度`;
                                    
                                traces.push({
                                    x: xValues,
                                    y: yValues,
                                    mode: 'lines+markers',
                                    name: traceName,
                                    line: { 
                                        shape: 'spline', 
                                        color: getColorByIndex(itemIndex * sortedFiscalYears.length + yearIndex),
                                        width: 2
                                    },
                                    marker: {
                                        color: getColorByIndex(itemIndex * sortedFiscalYears.length + yearIndex),
                                        size: 6
                                    },
                                    hovertemplate: `${traceName}<br>%{x}<br>${metric}: %{y${getHoverFormat(metric)}}${getHoverSuffix(metric)}<extra></extra>`
                                });
                            }
                        });
                    });
                }

            } else if (analysisTarget === 'keyword') {
                // キーワード集計分析
                if (displayMode === 'timeseries') {
                    // キーワード時系列分析（自治体フィルタ対応）
                    if (comparisonTarget === 'vs_total' && targetCity) {
                        // 自治体 vs 全体平均比較
                        const cityKeywordData = processDataForKeywordAnalysis(aggregatedMonthlyData, currentKeyword, targetCity);
                        const totalKeywordData = processDataForKeywordAnalysis(aggregatedMonthlyData, currentKeyword);
                        
                        // 自治体データ
                        const cityKey = `${targetCity}_${currentKeyword}`;
                        if (cityKeywordData[cityKey] && cityKeywordData[cityKey][metric] && cityKeywordData[cityKey][metric].length > 0) {
                            traces.push({
                                x: cityKeywordData[cityKey].Date,
                                y: cityKeywordData[cityKey][metric],
                                mode: 'lines+markers',
                                name: cityKeywordData[cityKey].displayName,
                                line: { 
                                    shape: 'spline',
                                    color: getColorByIndex(0),
                                    width: 3
                                },
                                marker: {
                                    color: getColorByIndex(0),
                                    size: 8
                                },
                                hovertemplate: `${cityKeywordData[cityKey].displayName}<br>%{x|%Y-%m-%d}<br>${metric}: %{y${getHoverFormat(metric)}}${getHoverSuffix(metric)}<extra></extra>`
                            });
                        }
                        
                        // 全体平均データ
                        const totalKey = `全体平均_${currentKeyword}`;
                        if (totalKeywordData[totalKey] && totalKeywordData[totalKey][metric] && totalKeywordData[totalKey][metric].length > 0) {
                            traces.push({
                                x: totalKeywordData[totalKey].Date,
                                y: totalKeywordData[totalKey][metric],
                                mode: 'lines+markers',
                                name: totalKeywordData[totalKey].displayName,
                                line: { 
                                    shape: 'spline',
                                    color: getColorByIndex(1),
                                    width: 3,
                                    dash: 'dash'
                                },
                                marker: {
                                    color: getColorByIndex(1),
                                    size: 8
                                },
                                hovertemplate: `${totalKeywordData[totalKey].displayName}<br>%{x|%Y-%m-%d}<br>${metric}: %{y${getHoverFormat(metric)}}${getHoverSuffix(metric)}<extra></extra>`
                            });
                        }
                    } else if (comparisonTarget === 'vs_cities' && selectedCities.length > 0) {
                        // 複数自治体比較
                        const multipleCitiesData = processMultipleCitiesKeywordAnalysis(aggregatedMonthlyData, currentKeyword, selectedCities);
                        
                        selectedCities.forEach((cityName, index) => {
                            const cityKey = `${cityName}_${currentKeyword}`;
                            if (multipleCitiesData[cityKey] && multipleCitiesData[cityKey][metric] && multipleCitiesData[cityKey][metric].length > 0) {
                                traces.push({
                                    x: multipleCitiesData[cityKey].Date,
                                    y: multipleCitiesData[cityKey][metric],
                                    mode: 'lines+markers',
                                    name: multipleCitiesData[cityKey].displayName,
                                    line: { 
                                        shape: 'spline',
                                        color: getColorByIndex(index),
                                        width: 3
                                    },
                                    marker: {
                                        color: getColorByIndex(index),
                                        size: 8
                                    },
                                    hovertemplate: `${multipleCitiesData[cityKey].displayName}<br>%{x|%Y-%m-%d}<br>${metric}: %{y${getHoverFormat(metric)}}${getHoverSuffix(metric)}<extra></extra>`
                                });
                            }
                        });
                    } else {
                        // 単一キーワード分析（自治体フィルタ対応）
                        let filteredData = aggregatedMonthlyData;
                        
                        // 自治体フィルタを適用
                        if (selectedCityFilters.length > 0) {
                            filteredData = aggregatedMonthlyData.filter(row => selectedCityFilters.includes(row.cityName));
                        }
                        
                        const keywordData = processDataForKeywordAnalysis(filteredData, currentKeyword);
                        const aggregateKey = `全体平均_${currentKeyword}`;
                        const aggregateData = keywordData[aggregateKey];
                        
                        if (aggregateData && aggregateData[metric] && aggregateData[metric].length > 0) {
                            const displayName = selectedCityFilters.length > 0 ? 
                                `[${selectedCityFilters.join('・')}] キーワード「${currentKeyword}」` : 
                                aggregateData.displayName;
                                
                            traces.push({
                                x: aggregateData.Date,
                                y: aggregateData[metric],
                                mode: 'lines+markers',
                                name: displayName,
                                line: { 
                                    shape: 'spline',
                                    color: getColorByIndex(0),
                                    width: 3
                                },
                                marker: {
                                    color: getColorByIndex(0),
                                    size: 8
                                },
                                hovertemplate: `${displayName}<br>%{x|%Y-%m-%d}<br>${metric}: %{y${getHoverFormat(metric)}}${getHoverSuffix(metric)}<extra></extra>`
                            });
                        }
                    }

                } else if (displayMode === 'annual_comparison') {
                    // キーワード年度比較（自治体フィルタ対応）
                    const monthsInOrder = ["4月", "5月", "6月", "7月", "8月", "9月", "10月", "11月", "12月", "1月", "2月", "3月"];
                    const selectedYears = getSelectedYears();
                    
                    if (selectedYears.length === 0) {
                        Plotly.purge(chartDiv.id);
                        chartDiv.innerHTML = `<h2>${metric}</h2><p style="text-align:center; color:#888;">比較する年度を選択してください。</p>`;
                        return;
                    }
                    
                    if (comparisonTarget === 'vs_total' && targetCity) {
                        // 自治体 vs 全体平均の年度比較
                        const cityAnnualData = aggregateKeywordForAnnualComparison(aggregatedMonthlyData, currentKeyword, selectedYears, targetCity);
                        const totalAnnualData = aggregateKeywordForAnnualComparison(aggregatedMonthlyData, currentKeyword, selectedYears);
                        
                        // 自治体データの年度比較
                        let cityFiscalYears = new Set();
                        for (let fiscalMonth = 0; fiscalMonth < 12; fiscalMonth++) {
                            if (cityAnnualData[fiscalMonth]) {
                                Object.keys(cityAnnualData[fiscalMonth]).forEach(year => {
                                    if (selectedYears.includes(year)) {
                                        cityFiscalYears.add(year);
                                    }
                                });
                            }
                        }
                        
                        Array.from(cityFiscalYears).sort().forEach((fiscalYear, yearIndex) => {
                            const yValues = [];
                            const xValues = [];
                            
                            for (let fiscalMonth = 0; fiscalMonth < 12; fiscalMonth++) {
                                const monthData = cityAnnualData[fiscalMonth] && cityAnnualData[fiscalMonth][fiscalYear];
                                if (monthData && monthData[metric] !== undefined && monthData[metric] !== null) {
                                    yValues.push(monthData[metric]);
                                    xValues.push(monthsInOrder[fiscalMonth]);
                                }
                            }

                            if (yValues.length > 0) {
                                traces.push({
                                    x: xValues,
                                    y: yValues,
                                    mode: 'lines+markers',
                                    name: `[${targetCity}] ${currentKeyword} ${fiscalYear}年度`,
                                    line: { 
                                        shape: 'spline', 
                                        color: getColorByIndex(yearIndex * 2),
                                        width: 3
                                    },
                                    marker: {
                                        color: getColorByIndex(yearIndex * 2),
                                        size: 8
                                    },
                                    hovertemplate: `[${targetCity}] ${currentKeyword} ${fiscalYear}年度<br>%{x}<br>${metric}: %{y${getHoverFormat(metric)}}${getHoverSuffix(metric)}<extra></extra>`
                                });
                            }
                        });
                        
                        // 全体平均データ
                        let totalFiscalYears = new Set();
                        for (let fiscalMonth = 0; fiscalMonth < 12; fiscalMonth++) {
                            if (totalAnnualData[fiscalMonth]) {
                                Object.keys(totalAnnualData[fiscalMonth]).forEach(year => {
                                    if (selectedYears.includes(year)) {
                                        totalFiscalYears.add(year);
                                    }
                                });
                            }
                        }
                        
                        Array.from(totalFiscalYears).sort().forEach((fiscalYear, yearIndex) => {
                            const yValues = [];
                            const xValues = [];
                            
                            for (let fiscalMonth = 0; fiscalMonth < 12; fiscalMonth++) {
                                const monthData = totalAnnualData[fiscalMonth] && totalAnnualData[fiscalMonth][fiscalYear];
                                if (monthData && monthData[metric] !== undefined && monthData[metric] !== null) {
                                    yValues.push(monthData[metric]);
                                    xValues.push(monthsInOrder[fiscalMonth]);
                                }
                            }

                            if (yValues.length > 0) {
                                traces.push({
                                    x: xValues,
                                    y: yValues,
                                    mode: 'lines+markers',
                                    name: `[全体平均] ${currentKeyword} ${fiscalYear}年度`,
                                    line: { 
                                        shape: 'spline', 
                                        color: getColorByIndex(yearIndex * 2 + 1),
                                        width: 3,
                                        dash: 'dash'
                                    },
                                    marker: {
                                        color: getColorByIndex(yearIndex * 2 + 1),
                                        size: 8
                                    },
                                    hovertemplate: `[全体平均] ${currentKeyword} ${fiscalYear}年度<br>%{x}<br>${metric}: %{y${getHoverFormat(metric)}}${getHoverSuffix(metric)}<extra></extra>`
                                });
                            }
                        });
                        
                    } else if (comparisonTarget === 'vs_cities' && selectedCities.length > 0) {
                        // 複数自治体の年度比較
                        const multipleCitiesAnnualData = aggregateMultipleCitiesKeywordForAnnualComparison(aggregatedMonthlyData, currentKeyword, selectedYears, selectedCities);
                        
                        selectedCities.forEach((cityName, cityIndex) => {
                            const cityAnnualData = multipleCitiesAnnualData[cityName];
                            
                            let cityFiscalYears = new Set();
                            for (let fiscalMonth = 0; fiscalMonth < 12; fiscalMonth++) {
                                if (cityAnnualData[fiscalMonth]) {
                                    Object.keys(cityAnnualData[fiscalMonth]).forEach(year => {
                                        if (selectedYears.includes(year)) {
                                            cityFiscalYears.add(year);
                                        }
                                    });
                                }
                            }
                            
                            Array.from(cityFiscalYears).sort().forEach((fiscalYear, yearIndex) => {
                                const yValues = [];
                                const xValues = [];
                                
                                for (let fiscalMonth = 0; fiscalMonth < 12; fiscalMonth++) {
                                    const monthData = cityAnnualData[fiscalMonth] && cityAnnualData[fiscalMonth][fiscalYear];
                                    if (monthData && monthData[metric] !== undefined && monthData[metric] !== null) {
                                        yValues.push(monthData[metric]);
                                        xValues.push(monthsInOrder[fiscalMonth]);
                                    }
                                }

                                if (yValues.length > 0) {
                                    traces.push({
                                        x: xValues,
                                        y: yValues,
                                        mode: 'lines+markers',
                                        name: `[${cityName}] ${currentKeyword} ${fiscalYear}年度`,
                                        line: { 
                                            shape: 'spline', 
                                            color: getColorByIndex(cityIndex * selectedYears.length + yearIndex),
                                            width: 3
                                        },
                                        marker: {
                                            color: getColorByIndex(cityIndex * selectedYears.length + yearIndex),
                                            size: 8
                                        },
                                        hovertemplate: `[${cityName}] ${currentKeyword} ${fiscalYear}年度<br>%{x}<br>${metric}: %{y${getHoverFormat(metric)}}${getHoverSuffix(metric)}<extra></extra>`
                                    });
                                }
                            });
                        });
                    } else {
                        // 単一キーワードの年度比較（自治体フィルタ対応）
                        let filteredData = aggregatedMonthlyData;
                        
                        // 自治体フィルタを適用
                        if (selectedCityFilters.length > 0) {
                            filteredData = aggregatedMonthlyData.filter(row => selectedCityFilters.includes(row.cityName));
                        }
                        
                        const keywordAnnualData = aggregateKeywordForAnnualComparison(filteredData, currentKeyword, selectedYears);

                        let allFiscalYears = new Set();
                        for (let fiscalMonth = 0; fiscalMonth < 12; fiscalMonth++) {
                            if (keywordAnnualData[fiscalMonth]) {
                                Object.keys(keywordAnnualData[fiscalMonth]).forEach(year => {
                                    if (selectedYears.includes(year)) {
                                        allFiscalYears.add(year);
                                    }
                                });
                            }
                        }
                        const sortedFiscalYears = Array.from(allFiscalYears).sort();

                        sortedFiscalYears.forEach((fiscalYear, yearIndex) => {
                            const yValues = [];
                            const xValues = [];
                            
                            for (let fiscalMonth = 0; fiscalMonth < 12; fiscalMonth++) {
                                const monthData = keywordAnnualData[fiscalMonth] && keywordAnnualData[fiscalMonth][fiscalYear];
                                if (monthData && monthData[metric] !== undefined && monthData[metric] !== null) {
                                    yValues.push(monthData[metric]);
                                    xValues.push(monthsInOrder[fiscalMonth]);
                                }
                            }

                            if (yValues.length > 0) {
                                const traceName = selectedCityFilters.length > 0 ? 
                                    `[${selectedCityFilters.join('・')}] キーワード「${currentKeyword}」${fiscalYear}年度` :
                                    `[全体平均] キーワード「${currentKeyword}」${fiscalYear}年度`;
                                    
                                traces.push({
                                    x: xValues,
                                    y: yValues,
                                    mode: 'lines+markers',
                                    name: traceName,
                                    line: { 
                                        shape: 'spline', 
                                        color: getColorByIndex(yearIndex),
                                        width: 3
                                    },
                                    marker: {
                                        color: getColorByIndex(yearIndex),
                                        size: 8
                                    },
                                    hovertemplate: `${traceName}<br>%{x}<br>${metric}: %{y${getHoverFormat(metric)}}${getHoverSuffix(metric)}<extra></extra>`
                                });
                            }
                        });
                    }
                }
            }

            // レイアウト設定
            if (displayMode === 'timeseries') {
                layout = {
                    title: '',
                    xaxis: { title: '日付', type: 'date' },
                    yaxis: { title: getAxisTitle(metric) },
                    hovermode: 'x unified',
                    margin: { t: 80, b: 50, l: 80, r: 40 },
                    legend: {
                        orientation: 'h', // 水平配置
                        x: 0,
                        y: 1.02, // グラフの上部に配置（外側）
                        xanchor: 'left', // 左揃え
                        yanchor: 'bottom', // 下揃え（y座標の基準）
                        font: { size: 10 }
                    }
                };
            } else {
                layout = {
                    title: '',
                    xaxis: { 
                        title: '月', 
                        tickmode: 'array', 
                        tickvals: ["4月", "5月", "6月", "7月", "8月", "9月", "10月", "11月", "12月", "1月", "2月", "3月"], 
                        ticktext: ["4月", "5月", "6月", "7月", "8月", "9月", "10月", "11月", "12月", "1月", "2月", "3月"],
                        categoryorder: 'array',
                        categoryarray: ["4月", "5月", "6月", "7月", "8月", "9月", "10月", "11月", "12月", "1月", "2月", "3月"]
                    },
                    yaxis: { title: getAxisTitle(metric) },
                    hovermode: 'x unified',
                    margin: { t: 80, b: 50, l: 80, r: 40 },
                    legend: {
                        orientation: 'h', // 水平配置
                        x: 0,
                        y: 1.02, // グラフの上部に配置（外側）
                        xanchor: 'left', // 左揃え
                        yanchor: 'bottom', // 下揃え（y座標の基準）
                        font: { size: 10 }
                    }
                };
            }

            if (traces.length === 0) {
                Plotly.purge(chartDiv.id);
                chartDiv.innerHTML = `<h2>${metric}</h2><p style="text-align:center; color:#888;">データがありません。</p>`;
                return;
            }
            
            Plotly.newPlot(chartDiv.id, traces, layout, { responsive: true });
        }

        // 複数商品対応のグラフ更新関数（非同期処理で最適化）
        async function updateChartsMultiple(selectedItemIds = null) {
            if (isProcessing) return;
            
            showLoading();
            
            try {
                const analysisTarget = analysisTargetSelector.value;
                const displayMode = displayModeSelector.value;
                const comparisonTarget = comparisonMode.value;
                const targetCity = targetCitySelector.value;
                const selectedCities = getSelectedCities();
                const selectedCityFilters = getSelectedCityFilters();
                
                if (analysisTarget === 'individual') {
                    if (!selectedItemIds) {
                        selectedItemIds = $('#itemSelector').val() || [];
                    }
                }

                // タイトル更新
                let titleText = 'ふるさと納税 返礼品 各指標 時系列分析レポート - 自治体フィルタ対応版';
                
                if (displayMode === 'annual_comparison') {
                    const selectedYears = getSelectedYears();
                    if (selectedYears.length > 0) {
                        titleText += ` - 年度比較（${selectedYears.join('・')}年度）`;
                    }
                }
                
                if (analysisTarget === 'keyword' && currentKeyword) {
                    if (comparisonTarget === 'vs_total' && targetCity) {
                        titleText += ` - ${targetCity} vs 全体平均: キーワード「${currentKeyword}」`;
                    } else if (comparisonTarget === 'vs_cities' && selectedCities.length > 0) {
                        titleText += ` - 複数自治体比較: キーワード「${currentKeyword}」`;
                    } else {
                        if (selectedCityFilters.length > 0) {
                            titleText += ` - ${selectedCityFilters.join('・')}: キーワード「${currentKeyword}」`;
                        } else {
                            titleText += ` - 全体平均: キーワード「${currentKeyword}」`;
                        }
                    }
                }

                document.querySelector('h1').textContent = titleText;

                // 各指標のグラフを非同期で更新（処理を分散）
                for (let i = 0; i < METRICS_TO_PLOT.length; i++) {
                    const metric = METRICS_TO_PLOT[i];
                    
                    // 小さな遅延を入れてブラウザがフリーズしないようにする
                    await new Promise(resolve => {
                        setTimeout(() => {
                            const chartDiv = document.getElementById('chart_' + metric);
                            let currentH2 = chartDiv.querySelector('h2');
                            if (!currentH2) {
                                currentH2 = document.createElement('h2');
                                chartDiv.prepend(currentH2);
                            }
                            currentH2.textContent = metric;

                            plotChart(metric, selectedItemIds);
                            resolve();
                        }, 50); // 50ms間隔で処理
                    });
                }
                
            } catch (error) {
                console.error('グラフ更新エラー:', error);
            }
            
            hideLoading();
        }

        function filterAndDisplayCharts() {
            if (isProcessing) return;
            
            const analysisTarget = analysisTargetSelector.value;
            const displayMode = displayModeSelector.value;
            const minSalesQuantity = parseInt(minSalesQuantitySelector.value);
            const selectedYears = getSelectedYears();
            const selectedCityFilters = getSelectedCityFilters();

            let filteredData = aggregatedMonthlyData.filter(row => {
                // 年度フィルタ
                if (displayMode === 'timeseries') {
                    const yearSelectorValue = yearSelector.value;
                    if (yearSelectorValue !== 'all' && row.FiscalYear != parseInt(yearSelectorValue)) {
                        return false;
                    }
                } else if (displayMode === 'annual_comparison') {
                    if (selectedYears.length > 0 && !selectedYears.includes(row.FiscalYear.toString())) {
                        return false;
                    }
                }
                
                // 自治体フィルタ（比較なしの場合のみ）
                if (comparisonMode.value === 'none' && selectedCityFilters.length > 0) {
                    if (!selectedCityFilters.includes(row.cityName)) {
                        return false;
                    }
                }
                
                const salesQuantity = row['売上件数'] || 0;
                if (salesQuantity < minSalesQuantity) {
                    return false;
                }
                
                // 高度な検索条件
                if (!matchesAdvancedSearch(row)) {
                    return false;
                }
                
                return true;
            });
            
            // 検索統計を更新
            updateSearchStats();
            
            if (analysisTarget === 'individual') {
                // 個別商品分析モード用にデータを整形
                processedDataForCurrentView = processDataForTimeseries(filteredData);
                updateItemSelector();
            } else {
                // キーワード集計モード - 年度変更時に統計も更新
                if (currentKeyword) {
                    handleKeywordInput();
                }
                updateChartsMultiple();
            }
        }

        // 初期化処理
        $(document).ready(function() {
            // イベントリスナー設定
            loadDataButton.addEventListener('click', handleFolderLoad);
            
            // Select2を初期化
            initializeSelect2();
            
            // 初期状態を設定
            handleAnalysisTargetChange();
            clearCharts();
        });
    </script>
</body>
</html>
