
let socket;
let prices = [];

function startAnalysis() {
    const symbol = document.getElementById("symbol").value;

    if (socket) {
        socket.close();
    }

    prices = [];

    socket = new WebSocket(
        "wss://ws.derivws.com/websockets/v3?app_id=1089"
    );

    socket.onopen = function () {

        socket.send(JSON.stringify({
            ticks_history: symbol,
            count: 100,
            end: "latest",
            start: 1,
            style: "ticks"
        }));

        socket.send(JSON.stringify({
            ticks: symbol,
            subscribe: 1
        }));
    };

    socket.onmessage = function(event) {

        const data = JSON.parse(event.data);

        if (data.history) {
            prices = data.history.prices.map(Number);
            updateAnalysis();
        }

        if (data.tick) {
            const price = Number(data.tick.quote);

            prices.push(price);

            if (prices.length > 100) {
                prices.shift();
            }

            document.getElementById("price").innerText =
                price.toFixed(2);

            updateAnalysis();
        }
    };

    socket.onerror = function() {
        document.getElementById("signal").innerText =
            "Connection error";
    };
}

function calculateEMA(data, period) {

    if (data.length < period) return null;

    const multiplier = 2 / (period + 1);

    let ema = data
        .slice(0, period)
        .reduce((a, b) => a + b, 0) / period;

    for (let i = period; i < data.length; i++) {
        ema =
            (data[i] - ema) * multiplier + ema;
    }

    return ema;
}

function calculateRSI(data, period = 14) {

    if (data.length <= period) return null;

    let gains = 0;
    let losses = 0;

    for (let i = 1; i <= period; i++) {

        const change = data[i] - data[i - 1];

        if (change > 0) {
            gains += change;
        } else {
            losses += Math.abs(change);
        }
    }

    let averageGain = gains / period;
    let averageLoss = losses / period;

    for (let i = period + 1; i < data.length; i++) {

        const change = data[i] - data[i - 1];

        const gain = change > 0 ? change : 0;
        const loss = change < 0 ? Math.abs(change) : 0;

        averageGain =
            ((averageGain * (period - 1)) + gain) / period;

        averageLoss =
            ((averageLoss * (period - 1)) + loss) / period;
    }

    if (averageLoss === 0) return 100;

    const rs = averageGain / averageLoss;

    return 100 - (100 / (1 + rs));
}

function updateAnalysis() {

    if (prices.length < 20) return;

    const ema9 = calculateEMA(prices, 9);
    const ema21 = calculateEMA(prices, 21);
    const rsi = calculateRSI(prices);

    let signal = "WAIT";

    if (ema9 > ema21 && rsi > 50 && rsi < 70) {
        signal = "UP TREND";
    }

    if (ema9 < ema21 && rsi < 50 && rsi > 30) {
        signal = "DOWN TREND";
    }

    document.getElementById("signal").innerHTML =
        signal +
        "<br><br>EMA 9: " + ema9.toFixed(2) +
        "<br>EMA 21: " + ema21.toFixed(2) +
        "<br>RSI: " + rsi.toFixed(2);
}
