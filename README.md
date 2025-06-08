<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>E-Commerce Product Listing</title>
    <!-- Tailwind CSS CDN for modern styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6; /* Light gray background */
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 1.5rem;
        }
        .product-grid {
            display: grid;
            gap: 1.5rem; /* Gap between grid items */
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); /* Responsive grid */
        }
        .product-card {
            background-color: #ffffff; /* White card background */
            border-radius: 0.75rem; /* Rounded corners */
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06); /* Subtle shadow */
            overflow: hidden; /* Ensure image corners are rounded */
            display: flex;
            flex-direction: column;
            transition: transform 0.2s ease-in-out; /* Smooth hover effect */
        }
        .product-card:hover {
            transform: translateY(-5px); /* Lift effect on hover */
        }
        .product-image {
            width: 100%;
            height: 200px; /* Fixed height for consistency */
            object-fit: cover; /* Cover the area, crop if necessary */
            border-bottom: 1px solid #e5e7eb; /* Light separator */
        }
        .product-info {
            padding: 1rem;
            flex-grow: 1; /* Allows info section to expand */
            display: flex;
            flex-direction: column;
            justify-content: space-between;
        }
        .product-title {
            font-size: 1.25rem; /* Larger title */
            font-weight: 600; /* Semi-bold */
            color: #1f2937; /* Dark gray */
            margin-bottom: 0.5rem;
        }
        .product-description {
            font-size: 0.9rem;
            color: #6b7280; /* Medium gray */
            margin-bottom: 0.75rem;
            flex-grow: 1; /* Allow description to take space */
        }
        .product-price {
            font-size: 1.125rem; /* Slightly larger price */
            font-weight: 700; /* Bold */
            color: #10b981; /* Green for price */
            margin-bottom: 1rem;
        }
        .add-to-cart-button {
            display: block;
            width: 100%;
            padding: 0.75rem 1rem;
            background-color: #4f46e5; /* Indigo */
            color: #ffffff; /* White text */
            font-weight: 600;
            text-align: center;
            border-radius: 0.5rem;
            transition: background-color 0.2s ease-in-out;
            cursor: pointer;
        }
        .add-to-cart-button:hover {
            background-color: #4338ca; /* Darker indigo on hover */
        }
        /* Message box styling */
        .message-box-container {
            position: fixed;
            top: 20px;
            right: 20px;
            z-index: 1000;
            display: none; /* Hidden by default */
        }
        .message-box {
            background-color: #4CAF50; /* Green */
            color: white;
            padding: 15px;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.2);
            opacity: 0;
            transition: opacity 0.5s ease-in-out;
        }
        .message-box.show {
            opacity: 1;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="text-3xl font-bold text-gray-800 text-center mb-8 rounded-lg p-4 bg-white shadow">Our Products</h1>

        <div id="product-list" class="product-grid">
            <!-- Product cards will be injected here by JavaScript -->
        </div>
    </div>

    <!-- Message Box HTML -->
    <div id="messageBoxContainer" class="message-box-container">
        <div id="messageBox" class="message-box"></div>
    </div>

    <script>
        // Data for products - in a real application, this would come from an API call
        const products = [
            {
                id: 1,
                name: 'Wireless Bluetooth Headphones',
                description: 'Experience immersive sound with active noise cancellation and comfortable earcups.',
                price: 99.99,
                imageUrl: 'https://placehold.co/400x200/4F46E5/FFFFFF?text=Headphones'
            },
            {
                id: 2,
                name: 'Smartwatch with Heart Rate Monitor',
                description: 'Track your fitness, receive notifications, and monitor your health metrics 24/7.',
                price: 149.99,
                imageUrl: 'https://placehold.co/400x200/10B981/FFFFFF?text=Smartwatch'
            },
            {
                id: 3,
                name: 'Portable Power Bank 20000mAh',
                description: 'Keep your devices charged on the go with this high-capacity portable charger.',
                price: 39.99,
                imageUrl: 'https://placehold.co/400x200/EF4444/FFFFFF?text=Powerbank'
            },
            {
                id: 4,
                name: 'Ergonomic Office Chair',
                description: 'Designed for ultimate comfort and support during long working hours.',
                price: 249.00,
                imageUrl: 'https://placehold.co/400x200/3B82F6/FFFFFF?text=Office+Chair'
            },
            {
                id: 5,
                name: '4K Ultra HD Smart TV',
                description: 'Stunning visuals and smart features bring your entertainment to life.',
                price: 699.00,
                imageUrl: 'https://placehold.co/400x200/F59E0B/FFFFFF?text=Smart+TV'
            },
            {
                id: 6,
                name: 'Compact Espresso Machine',
                description: 'Brew delicious espresso at home with this sleek and easy-to-use machine.',
                price: 199.50,
                imageUrl: 'https://placehold.co/400x200/9333EA/FFFFFF?text=Espresso+Machine'
            }
        ];

        // Get the product list container
        const productListContainer = document.getElementById('product-list');
        const messageBoxContainer = document.getElementById('messageBoxContainer');
        const messageBox = document.getElementById('messageBox');

        // Function to display a message
        function showMessage(message, type = 'success') {
            messageBox.textContent = message;
            messageBox.className = 'message-box show'; // Reset and add show class

            // Set background color based on type
            if (type === 'success') {
                messageBox.style.backgroundColor = '#4CAF50'; // Green
            } else if (type === 'error') {
                messageBox.style.backgroundColor = '#f44336'; // Red
            } else {
                messageBox.style.backgroundColor = '#2196F3'; // Blue (info)
            }

            messageBoxContainer.style.display = 'block'; // Show the container

            // Hide the message after 3 seconds
            setTimeout(() => {
                messageBox.classList.remove('show');
                // Allow transition to finish before hiding container
                setTimeout(() => {
                    messageBoxContainer.style.display = 'none';
                }, 500); // Match transition duration
            }, 3000);
        }

        // Function to render product cards
        function renderProducts() {
            products.forEach(product => {
                const productCard = document.createElement('div');
                productCard.className = 'product-card';
                productCard.innerHTML = `
                    <img src="${product.imageUrl}" alt="${product.name}" class="product-image">
                    <div class="product-info">
                        <div>
                            <h2 class="product-title">${product.name}</h2>
                            <p class="product-description">${product.description}</p>
                        </div>
                        <div class="mt-auto"> <!-- Push price and button to bottom -->
                            <p class="product-price">$${product.price.toFixed(2)}</p>
                            <button class="add-to-cart-button" data-product-id="${product.id}">Add to Cart</button>
                        </div>
                    </div>
                `;
                productListContainer.appendChild(productCard);
            });

            // Add event listeners to "Add to Cart" buttons
            document.querySelectorAll('.add-to-cart-button').forEach(button => {
                button.addEventListener('click', (event) => {
                    const productId = event.target.dataset.productId;
                    const productName = products.find(p => p.id == productId)?.name || 'Unknown Product';
                    showMessage(`${productName} added to cart!`);
                    // In a real app, this would send an API request to add to a shopping cart service
                    console.log(`Product with ID ${productId} added to cart.`);
                });
            });
        }

        // Render products when the DOM is fully loaded
        document.addEventListener('DOMContentLoaded', renderProducts);
    </script>
</body>
</html>
