.pallet-details-container {
    padding: 30px;
    font-family: Verdana;
}

.bay-header {
    color: #00008B;
    font-size: 55px;
    font-weight: bold;
    margin-bottom: 30px;
}

.section-header {
    color: #00008B;
    font-size: 40px;
    font-weight: bold;
    margin: 25px 0 15px 0;
}

.pallet-grid {
    width: 100%;
    border-collapse: collapse;
    margin: 20px 0;
    font-size: 24px;
}

.grid-header {
    background-color: #00008B;
    color: white;
    padding: 15px;
    text-align: left;
    font-weight: bold;
}

.pallet-number, .pack-order, .scan-time {
    padding: 12px 15px;
    border-bottom: 1px solid #ddd;
}

.pallet-number {
    font-weight: bold;
    color: #00008B;
}

.progress-text {
    font-size: 30px;
    color: #00008B;
    font-weight: bold;
    display: block;
    margin: 25px 0;
}

.back-button {
    display: inline-block;
    background-color: #009225;
    color: white !important;
    padding: 15px 30px;
    font-size: 24px;
    font-weight: bold;
    text-decoration: none;
    border-radius: 8px;
    margin-top: 30px;
    transition: all 0.3s ease;
}

.back-button:hover {
    background-color: #00A828;
    transform: scale(1.02);
    box-shadow: 0 2px 8px rgba(0,0,0,0.2);
}

/* Alternating row colors */
.pallet-grid tr:nth-child(even) {
    background-color: #f9f9f9;
}

.pallet-grid tr:hover {
    background-color: #f0f0f0;
}

/* Summary section styling */
.summary-section {
    margin: 30px 0;
    padding: 20px;
    background-color: #f5f5f5;
    border-radius: 8px;
}

/* Responsive adjustments */
@media (max-width: 768px) {
    .bay-header {
        font-size: 40px;
    }
    
    .section-header {
        font-size: 30px;
    }
    
    .pallet-grid {
        font-size: 18px;
    }
    
    .back-button {
        font-size: 18px;
        padding: 12px 24px;
    }
}
