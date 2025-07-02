<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI Team Workload Distributor</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        input[type="range"]::-webkit-slider-runnable-track { height: 8px; border-radius: 4px; }
        input[type="range"]::-moz-range-track { height: 8px; border-radius: 4px; }
        input[type="range"]::-webkit-slider-thumb {
            -webkit-appearance: none; appearance: none;
            width: 24px; height: 24px; border-radius: 50%;
            cursor: pointer; margin-top: -8px;
        }
        input[type="range"]::-moz-range-thumb {
            width: 24px; height: 24px; border-radius: 50%; cursor: pointer;
        }
    </style>
</head>
<body class="bg-gray-100 flex items-center justify-center min-h-screen">
    <div class="w-full max-w-4xl mx-auto p-4 md:p-8 bg-white rounded-2xl shadow-lg">
        <div class="text-center mb-8">
            <h1 class="text-3xl md:text-4xl font-bold text-gray-800">AI Team Workload Balancer</h1>
            <p class="text-gray-500 mt-2">Adjust one person's workload and see how it affects the team.</p>
        </div>
        <div class="bg-indigo-600 text-white p-4 rounded-xl text-center mb-8 shadow-md">
            <h2 class="text-xl font-semibold">Total Mandated AI Workload</h2>
            <p id="total-workload" class="text-4xl font-bold">21.0%</p>
        </div>
        <div id="team-container" class="grid grid-cols-1 md:grid-cols-3 gap-6"></div>
    </div>
    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const teamContainer = document.getElementById('team-container');
            const totalWorkload = 21;
            const colorMapping = {
                blue:   { 600: '#2563EB' },
                green:  { 600: '#059669' },
                purple: { 600: '#7C3AED' }
            };
            const team = [
                { name: 'Amir', color: 'blue', workload: 7.0 },
                { name: 'Silvan', color: 'green', workload: 7.0 },
                { name: 'Stipo', color: 'purple', workload: 7.0 }
            ];
            team.forEach((member, index) => {
                const memberCard = createMemberCard(member, index);
                teamContainer.appendChild(memberCard);
            });
            const sliders = document.querySelectorAll('input[type="range"]');
            sliders.forEach((slider, index) => {
                slider.addEventListener('input', (event) => {
                    handleSliderChange(index, parseFloat(event.target.value));
                });
            });
            function handleSliderChange(changedIndex, newWorkload) {
                const oldWorkload = team[changedIndex].workload;
                const delta = newWorkload - oldWorkload;
                team[changedIndex].workload = newWorkload;
                const changePerOther = -delta / 2;
                team.forEach((member, index) => {
                    if (index !== changedIndex) {
                        member.workload += changePerOther;
                    }
                });
                updateUI();
            }
            function updateUI() {
                team.forEach((member, index) => {
                    const workloadEl = document.getElementById(`workload-value-${index}`);
                    const sliderEl = document.getElementById(`workload-slider-${index}`);
                    const circleEl = document.getElementById(`workload-circle-${index}`);
                    const currentWorkload = Math.max(0, member.workload);
                    workloadEl.textContent = `${currentWorkload.toFixed(1)}%`;
                    if (document.activeElement !== sliderEl) {
                       sliderEl.value = currentWorkload;
                    }
                    const scale = Math.max(0.1, currentWorkload / (totalWorkload / 2));
                    circleEl.style.transform = `scale(${scale})`;
                });
            }
            function createMemberCard(member, index) {
                const card = document.createElement('div');
                card.className = `bg-gray-50 p-6 rounded-xl shadow-sm border border-gray-200 flex flex-col items-center`;
                const color = member.color;
                card.innerHTML = `
                    <div class="relative w-32 h-32 flex items-center justify-center mb-4">
                        <div class="absolute inset-0 bg-${color}-100 rounded-full animate-pulse"></div>
                        <div id="workload-circle-${index}" class="relative w-full h-full bg-${color}-500 rounded-full shadow-inner transition-transform duration-200 ease-out" style="transform: scale(0.66);"></div>
                    </div>
                    <h3 class="text-2xl font-semibold text-gray-700">${member.name}</h3>
                    <p id="workload-value-${index}" class="text-4xl font-bold text-${color}-600 my-2">${member.workload.toFixed(1)}%</p>
                    <input id="workload-slider-${index}" type="range" min="0" max="${totalWorkload}" value="${member.workload}" step="0.1" class="w-full h-2 bg-${color}-200 rounded-lg appearance-none cursor-pointer mt-4">
                `;
                const style = document.createElement('style');
                style.innerHTML = `#workload-slider-${index}::-webkit-slider-thumb { background: ${colorMapping[color][600]}; } #workload-slider-${index}::-moz-range-thumb { background: ${colorMapping[color][600]}; }`;
                document.head.appendChild(style);
                return card;
            }
            updateUI();
        });
    </script>
</body>
</html>
