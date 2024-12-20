<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>돌 시세 추적기</title>
    <script src="https://cdn.jsdelivr.net/npm/apexcharts"></script>
    <style>
        /* 다크모드 스타일 */
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #2e2e2e;
            color: #ffffff;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
        }

        h1 {
            font-size: 2.5rem;
            margin-bottom: 20px;
            text-align: center;
        }

        .price-info {
            font-size: 2rem;
            margin-top: 20px;
            font-weight: bold;
            text-align: center;
        }

        .real-time-price {
            font-size: 1.5rem;
            margin-top: 10px;
            text-align: center;
        }

        .arrow {
            font-size: 1.5rem;
            margin-top: 10px;
            text-align: center;
        }

        .up {
            color: red;
        }

        .down {
            color: blue;
        }

        #chart {
            width: 80%;
            height: 300px;
            margin-top: 30px;
        }

        @media (max-width: 768px) {
            h1 {
                font-size: 2rem;
            }
            .price-info, .real-time-price {
                font-size: 1.2rem;
            }
            #chart {
                width: 95%;
                height: 250px;
            }
        }
    </style>
</head>
<body>
    <h1>돌 시세</h1>
    <div class="arrow" id="price-arrow">▲ 0원 (0%)</div>
    <div class="real-time-price">실시간 가격: <span id="real-time">0</span>원</div>
    <div id="chart"></div>

    <script>
        let price = 0;
        const priceHistory = [0]; // 가격 변동 기록
        const timeStamps = [0]; // 시간 스탬프

        // 차트 설정
        const options = {
            chart: {
                type: 'area',
                height: 300,
                zoom: {
                    enabled: false
                }
            },
            dataLabels: {
                enabled: false
            },
            stroke: {
                curve: 'smooth'
            },
            series: [{
                name: '돌 시세',
                data: priceHistory
            }],
            xaxis: {
                categories: timeStamps,
                title: {
                    text: '시간',
                    style: {
                        color: '#ffffff'
                    }
                }
            },
            yaxis: {
                title: {
                    text: '가격',
                    style: {
                        color: '#ffffff'
                    }
                }
            },
            colors: ['#FF4560'],
            theme: {
                mode: 'dark', // 다크 모드 설정
            }
        };

        const chart = new ApexCharts(document.querySelector("#chart"), options);
        chart.render();

        // 시세 변동 업데이트
        function updatePrice() {
            const randomChange = (Math.random() * 2000) - 1000; // -1000원에서 1000원 사이 랜덤 변동
            price += randomChange;

            if (price < 0) price = 0; // 최소값 0원

            // 실시간 가격 업데이트
            const realTimePrice = Math.floor(Math.random() * 15000); // 0~15000원 사이 실시간 시세
            document.getElementById('real-time').textContent = realTimePrice;

            // 가격 변화 화살표 업데이트
            const priceArrow = document.getElementById('price-arrow');
            const priceChange = Math.floor(realTimePrice - price);
            const percentageChange = ((priceChange / price) * 100).toFixed(1);

            if (priceChange > 0) {
                priceArrow.textContent = `▲ ${priceChange}원 (+${percentageChange}%)`;
                priceArrow.classList.remove('down');
                priceArrow.classList.add('up');
            } else if (priceChange < 0) {
                priceArrow.textContent = `▼ ${Math.abs(priceChange)}원 (-${Math.abs(percentageChange)}%)`;
                priceArrow.classList.remove('up');
                priceArrow.classList.add('down');
            } else {
                priceArrow.textContent = `▲ 0원 (0%)`;
                priceArrow.classList.remove('up', 'down');
            }

            // 차트 업데이트
            priceHistory.push(realTimePrice);  // 가격 이력에 추가
            timeStamps.push(timeStamps.length);  // X축 레이블에 시간(또는 업데이트 횟수) 추가
            chart.updateSeries([{
                data: priceHistory
            }]);  // 차트 데이터에 새 시세 값 반영
            chart.updateOptions({
                xaxis: {
                    categories: timeStamps
                }
            });

            // 3초에서 15초 사이 랜덤으로 다음 가격 변동 업데이트
            setTimeout(updatePrice, Math.random() * (15000 - 3000) + 3000);
        }

        // 가격 변동 시작
        updatePrice();
    </script>
</body>
</html>
