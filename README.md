<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello Me 🐰 | Affordable Joy</title>
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;600&family=Outfit:wght@700&display=swap" rel="stylesheet">
    <style>
        :root {
            --periwinkle: #a1b6f7;
            --rose: #ffb7ce;
            --mint: #b9fbc0;
            --text: #5d5d5d;
            --card-bg: #ffffff;
            --nav-bg: #ffffff;
        }

        [data-theme="dark"] {
            --periwinkle: #2d3436;
            --rose: #ef5777;
            --text: #ffffff;
            --card-bg: #3d4852;
            --nav-bg: #1e272e;
        }

        body {
            margin: 0;
            font-family: 'Fredoka', sans-serif;
            background-color: var(--periwinkle);
            color: var(--text);
            transition: background 0.3s ease;
            min-height: 100vh;
            overflow-x: hidden;
        }

        nav {
            background-color: var(--nav-bg);
            padding: 10px 5%;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 4px solid var(--rose);
            position: sticky;
            top: 0;
            z-index: 1000;
        }

        .logo-font { font-family: 'Outfit', sans-serif; color: var(--rose); font-size: 1.6rem; }
        .nav-controls { display: flex; align-items: center; gap: 12px; }

        /* --- SHOPPING BAG SIDEBAR --- */
        #bag-sidebar {
            position: fixed;
            right: -350px;
            top: 0;
            width: 300px;
            height: 100%;
            background: var(--card-bg);
            box-shadow: -5px 0 15px rgba(0,0,0,0.1);
            transition: 0.3s ease;
            z-index: 2000;
            padding: 20px;
            display: flex;
            flex-direction: column;
        }

        #bag-sidebar.open { right: 0; }

        .bag-header { display: flex; justify-content: space-between; align-items: center; border-bottom: 2px solid var(--rose); padding-bottom: 10px; }
        
        #bag-items-list { flex-grow: 1; overflow-y: auto; margin-top: 20px; }

        .bag-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
            padding: 10px;
            background: rgba(161, 182, 247, 0.1);
            border-radius: 15px;
        }

        .remove-btn { color: #ff4757; cursor: pointer; font-size: 0.8rem; border: none; background: none; }

        .bag-footer { border-top: 2px solid var(--rose); padding-top: 20px; }

        /* --- UI COMPONENTS --- */
        .container { max-width: 1100px; margin: 0 auto; padding: 20px; }
        .grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(260px, 1fr)); gap: 20px; }
        
        .card {
            background: var(--card-bg); 
            padding: 25px; 
            border-radius: 30px;
            box-shadow: 0 6px 0 var(--rose); 
            text-align: center; 
            position: relative;
        }

        .badge { background: var(--mint); color: #2d3436; font-size: 0.65rem; padding: 4px 10px; border-radius: 20px; position: absolute; top: 15px; right: 15px; font-weight: bold; }
        .item-emoji { font-size: 55px; margin: 15px 0; display: block; }

        .btn {
            background: var(--rose); color: white; padding: 12px; border-radius: 20px;
            border: none; cursor: pointer; font-weight: 600; width: 100%;
        }

        .price-tag { font-weight: bold; color: var(--rose); font-size: 1.5rem; margin: 10px 0; }

        .cart-icon { position: relative; font-size: 1.4rem; cursor: pointer; }
        #cart-count {
            position: absolute; top: -5px; right: -5px;
            background: #ff4757; color: white; font-size: 0.65rem;
            min-width: 18px; height: 18px; display: flex; align-items: center; 
            justify-content: center; border-radius: 50%; border: 2px solid white;
        }

        select, .theme-toggle { border-radius: 8px; padding: 6px; border: 1.5px solid var(--rose); background: var(--card-bg); color: var(--text); cursor: pointer; }

    </style>
</head>
<body>

<div id="bag-sidebar">
    <div class="bag-header">
        <h2 style="margin:0;">My Bag 🛒</h2>
        <button onclick="toggleBag()" style="background:none; border:none; font-size:1.5rem; cursor:pointer; color:var(--text);">×</button>
    </div>
    <div id="bag-items-list">
        </div>
    <div class="bag-footer">
        <h3 id="total-price-display">Total: ₦0</h3>
        <button class="btn" onclick="alert('Thank you! Orders are currently taken in person at school.')">Checkout</button>
    </div>
</div>

<nav>
    <div class="logo-font">hello me 🐰</div>
    <div class="nav-controls">
        <select id="currency-select" onchange="updateCurrency()">
            <option value="NGN">NGN (₦)</option>
            <option value="USD">USD ($)</option>
        </select>
        <button class="theme-toggle" onclick="toggleTheme()">🌓</button>
        <div class="cart-icon" onclick="toggleBag()">
            🛒 <span id="cart-count">0</span>
        </div>
    </div>
</nav>

<div class="container">
    <div style="text-align: center; padding: 30px 0; color: white;">
        <h1 style="margin:0;">Handmade Joy ✨</h1>
        <p>Everything under ₦500</p>
    </div>
    <div class="grid" id="product-grid"></div>
</div>

<script>
    let currentCurrency = 'NGN';
    const rate = 1500; 
    let bag = [];

    const products = [
        { id: 1, name: "Stress Ball", price: 150, emoji: "🎾" },
        { id: 2, name: "Beaded Necklace", price: 450, emoji: "📿" },
        { id: 3, name: "Octopus Keychain", price: 0, emoji: "🐙" },
        { id: 4, name: "Beaded Bracelet", price: 300, emoji: "✨" }
    ];

    function renderProducts() {
        const grid = document.getElementById('product-grid');
        grid.innerHTML = products.map(p => {
            let displayPrice = p.price === 0 ? "FREE" : 
                (currentCurrency === 'NGN' ? `₦${p.price}` : `$${(p.price / rate).toFixed(2)}`);
            
            return `
                <div class="card">
                    <div class="badge">HANDMADE</div>
                    <span class="item-emoji">${p.emoji}</span>
                    <h3>${p.name}</h3>
                    <div class="price-tag">${displayPrice}</div>
                    <button class="btn" onclick="addToBag(${p.id})">Add to Bag</button>
                </div>
            `;
        }).join('');
    }

    function addToBag(productId) {
        const item = products.find(p => p.id === productId);
        bag.push(item);
        updateBagUI();
        if(!document.getElementById('bag-sidebar').classList.contains('open')) {
            toggleBag();
        }
    }

    function removeFromBag(index) {
        bag.splice(index, 1);
        updateBagUI();
    }

    function updateBagUI() {
        document.getElementById('cart-count').innerText = bag.length;
        const list = document.getElementById('bag-items-list');
        let total = 0;

        list.innerHTML = bag.map((item, index) => {
            total += item.price;
            let itemPrice = item.price === 0 ? "FREE" : 
                (currentCurrency === 'NGN' ? `₦${item.price}` : `$${(item.price / rate).toFixed(2)}`);
            
            return `
                <div class="bag-item">
                    <span>${item.emoji} ${item.name}</span>
                    <span>${itemPrice} <button class="remove-btn" onclick="removeFromBag(${index})">✕</button></span>
                </div>
            `;
        }).join('');

        const totalDisplay = total === 0 ? "FREE" : 
            (currentCurrency === 'NGN' ? `₦${total}` : `$${(total / rate).toFixed(2)}`);
        document.getElementById('total-price-display').innerText = `Total: ${totalDisplay}`;
    }

    function toggleBag() {
        document.getElementById('bag-sidebar').classList.toggle('open');
    }

    function toggleTheme() {
        document.body.setAttribute('data-theme', document.body.getAttribute('data-theme') === 'dark' ? 'light' : 'dark');
    }

    function updateCurrency() {
        currentCurrency = document.getElementById('currency-select').value;
        renderProducts();
        updateBagUI();
    }

    renderProducts();
</script>

</body>
</html>
