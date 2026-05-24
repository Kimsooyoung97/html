<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CPB V 2026 Achievement UI</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@400;700;900&display=swap');
        body { font-family: 'Noto Sans KR', sans-serif; background-color: #2c3e50; margin: 0; display: flex; justify-content: center; align-items: center; min-height: 100vh; }
        
        .app-container { width: 400px; height: 750px; position: relative; overflow: hidden; background: #e2e8f0; box-shadow: 0 0 50px rgba(0,0,0,0.5); }
        .view { display: none; width: 100%; height: 100%; position: absolute; top: 0; left: 0; background: #e2e8f0; }
        .view.active { display: block; }

        /* 공통 스타일 */
        .pink-text { color: #ff3399; font-weight: 900; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
        
        /* 레드닷: 클릭 기능 및 시각적 효과 추가 */
        .red-dot { 
            position: absolute; top: 0; right: 0; width: 8px; height: 8px; 
            background: #ef4444; border-radius: 50%; border: 1.5px solid white; z-index: 10; 
            cursor: pointer; transition: transform 0.1s ease-out;
        }
        .red-dot:active { transform: scale(0.6); } /* 클릭 시 살짝 작아지는 효과 */
        
        /* 재화 바 */
        .resource-bar { background: linear-gradient(90deg, #1e293b 0%, #334155 50%, #1e293b 100%); border-top: 1px solid #475569; border-bottom: 2px solid #0f172a; height: 32px; }
        
        /* 업적 상단 탭 */
        .tab-active { background-color: #0c4a9e; border-bottom: 2px solid #60a5fa; color: white; font-weight: bold; box-shadow: inset 0 -2px 5px rgba(0,0,0,0.2); }
        .tab-inactive { background-color: #e2e8f0; color: #64748b; font-weight: bold; }
        .tab-clickable { cursor: pointer; transition: background-color 0.2s; }
        .tab-clickable:hover { background-color: #ffffff; }

        /* 업적 카테고리 탭 */
        .achieve-cat { flex: 1; display: flex; flex-direction: column; align-items: center; justify-content: center; background-color: #1e293b; border-right: 1px solid #0f172a; cursor: pointer; transition: all 0.2s; opacity: 0.6; }
        .achieve-cat.active { flex: 1.5; background-color: #0c4a9e; border-x: 1px solid #3b82f6; opacity: 1; box-shadow: inset 0 0 20px rgba(0,0,0,0.3); }
        .achieve-cat .cat-title { color: #94a3b8; font-weight: bold; font-size: 11px; margin-bottom: 8px; }
        .achieve-cat.active .cat-title { color: white; font-size: 14px; text-shadow: 1px 1px 2px black; }
        .achieve-cat .cat-circle { width: 50px; height: 50px; border-radius: 50%; border: 2px solid #475569; display: flex; flex-direction: column; align-items: center; justify-content: center; background: #0f172a; }
        .achieve-cat.active .cat-circle { width: 75px; height: 75px; border: 4px solid #1d4ed8; background: #1e3a8a; }
        
        .achieve-cat .inner-wrap { display: flex; flex-direction: row; align-items: baseline; gap: 1px; }
        .achieve-cat.active .inner-wrap { display: flex; flex-direction: row; align-items: baseline; gap: 1px; }
        .cat-val { color: #94a3b8; font-size: 9px; font-weight: bold; letter-spacing: -0.5px; }
        .achieve-cat.active .cat-val { color: #bef264; font-weight: 900; font-size: 13px; line-height: 1; letter-spacing: -0.5px; }
        .cat-max { color: #94a3b8; font-size: 7px; font-weight: bold; letter-spacing: -0.5px; }
        .achieve-cat.active .cat-max { color: #16a34a; font-size: 9px; font-weight: 900; letter-spacing: -0.5px; }

        /* 업적 리스트 내부 UI */
        .skew-bg { clip-path: polygon(0 0, 100% 0, 75% 100%, 0% 100%); }
        .bg-cyan-progress { background: linear-gradient(90deg, #06b6d4 0%, #67e8f9 100%); }
        .bg-cyan-yellow-progress { background: linear-gradient(90deg, #22d3ee 0%, #facc15 100%); }
        
        .achieve-list-container { display: none; }
        .achieve-list-container.active { display: block; animation: fadeIn 0.3s; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }
    </style>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
</head>
<body>

    <div class="app-container border-4 border-slate-800">
        
        <div id="view-achievement" class="view active bg-slate-300">
            
            <div class="h-12 bg-[#d35400] flex justify-between items-center px-3 pt-1 z-30 relative shadow-sm">
                <div class="w-8"></div>
                <div class="bg-[#a04000] text-white text-[11px] font-bold px-6 py-1.5 rounded-full flex items-center gap-2 border border-[#e67e22] shadow-inner">
                    도전 과제 <i class="fa-solid fa-caret-down text-[10px]"></i>
                </div>
                <div class="flex gap-2">
                    <div class="w-7 h-7 bg-white/20 rounded flex items-center justify-center text-white text-sm"><i class="fa-solid fa-comment-dots"></i></div>
                    <div class="w-7 h-7 bg-white/20 rounded flex items-center justify-center text-white text-sm relative">
                        <div class="red-dot" onclick="this.remove(); event.stopPropagation();"></div>
                        <i class="fa-solid fa-envelope"></i>
                    </div>
                </div>
            </div>

            <div class="resource-bar w-full flex text-white text-[10px] font-bold z-20 relative shadow-md">
                <div class="w-8 flex items-center justify-center bg-slate-800 border-r border-slate-600"><i class="fa-solid fa-caret-down text-slate-400"></i></div>
                <div class="flex-[1.2] flex items-center px-1.5 border-r border-slate-600 justify-between">
                    <div class="flex items-center gap-1"><div class="w-[14px] h-[14px] bg-blue-700 rounded-full flex items-center justify-center text-[8px] border border-blue-300 shadow">P</div> <span>999,999,999</span></div>
                    <div class="w-3 h-3 bg-slate-300 rounded text-slate-800 flex items-center justify-center text-[10px]">+</div>
                </div>
                <div class="flex-1 flex items-center px-1.5 border-r border-slate-600 justify-between">
                    <div class="flex items-center gap-1"><i class="fa-solid fa-star text-yellow-400 drop-shadow"></i> <span>999,999,999</span></div>
                    <div class="w-3 h-3 bg-slate-300 rounded text-slate-800 flex items-center justify-center text-[10px]">+</div>
                </div>
                <div class="flex-[1.1] flex items-center px-1.5 justify-between">
                    <div class="flex items-center gap-1"><i class="fa-solid fa-baseball text-white drop-shadow"></i> <span>999,999,999</span></div>
                    <div class="w-3 h-3 bg-slate-300 rounded text-slate-800 flex items-center justify-center text-[10px]">+</div>
                </div>
            </div>

            <div class="flex text-[11px] h-9 bg-slate-200 border-b border-slate-300 z-10 relative">
                <div class="flex-1 flex items-center justify-center tab-inactive tab-clickable relative">
                    일일 미션<div class="red-dot" style="top:4px; right:12px;" onclick="this.remove(); event.stopPropagation();"></div>
                </div>
                <div class="flex-1 flex items-center justify-center tab-active">업적</div>
                <div class="flex-1 flex items-center justify-center tab-inactive tab-clickable">반복 미션</div>
                <div class="flex-1 flex items-center justify-center tab-inactive tab-clickable">엠블럼</div>
            </div>

            <div class="flex h-[115px] bg-[#0f172a] shadow-lg relative z-10">
                <div id="tab-tutorial" onclick="switchCategory('tutorial')" class="achieve-cat">
                    <span class="cat-title">튜토리얼</span>
                    <div class="cat-circle"><div class="inner-wrap"><span class="cat-val">9,999</span><span class="cat-max">/9,999</span></div></div>
                </div>
                <div id="tab-play" onclick="switchCategory('play')" class="achieve-cat active">
                    <span class="cat-title">플레이</span>
                    <div class="cat-circle"><div class="inner-wrap"><span class="cat-val">9,999</span><span class="cat-max">/9,999</span></div></div>
                </div>
                <div id="tab-growth" onclick="switchCategory('growth')" class="achieve-cat">
                    <span class="cat-title">성장</span>
                    <div class="cat-circle"><div class="inner-wrap"><span class="cat-val">9,999</span><span class="cat-max">/9,999</span></div></div>
                </div>
                <div id="tab-etc" onclick="switchCategory('etc')" class="achieve-cat relative">
                    <div class="red-dot" style="top: 10px; right: 10px;" onclick="this.remove(); event.stopPropagation();"></div>
                    <span class="cat-title">기타</span>
                    <div class="cat-circle"><div class="inner-wrap"><span class="cat-val">9,999</span><span class="cat-max">/9,999</span></div></div>
                </div>
            </div>

            <div class="flex justify-between items-center px-4 py-1.5 bg-[#0c4a9e] border-y border-blue-800 shadow-md z-10 relative">
                <span class="text-white text-[12px] font-bold tracking-wide text-shadow">업적 리스트</span>
                <button onclick="receiveAll()" class="bg-[#581c87] text-white text-[10px] px-4 py-1.5 rounded font-bold border border-[#9333ea] hover:bg-purple-700 transition-colors shadow-inner">모두 받기</button>
            </div>

            <div class="h-[445px] overflow-y-auto no-scrollbar bg-slate-300 p-1 pb-2">
                <div id="list-tutorial" class="achieve-list-container space-y-[2px]"></div>
                <div id="list-play" class="achieve-list-container active space-y-[2px]"></div>
                <div id="list-growth" class="achieve-list-container space-y-[2px]"></div>
                <div id="list-etc" class="achieve-list-container space-y-[2px]"></div>
            </div>

            <div onclick="console.log('뒤로가기 클릭됨')" class="absolute bottom-0 w-full bg-[#c2410c] py-3 text-center text-white text-[13px] font-bold cursor-pointer z-50 shadow-[0_-5px_15px_rgba(0,0,0,0.4)] hover:bg-[#a0350a] active:bg-[#7a2807] transition-colors">◀ 뒤로 가기</div>
            
            <div id="reward-pop" class="absolute inset-0 bg-black/80 hidden items-center justify-center p-6 z-[100]">
                <div class="bg-white rounded-xl w-full overflow-hidden border-2 border-slate-400 shadow-2xl">
                    <div class="bg-slate-800 py-3 text-center text-white font-bold text-xs relative">보상 획득!<button onclick="document.getElementById('reward-pop').style.display='none'" class="absolute right-3 top-2 text-gray-400 text-lg hover:text-white">✕</button></div>
                    <div class="p-6 bg-gray-50 flex justify-center"><div class="bg-white border-2 border-slate-200 p-4 rounded-lg text-center shadow-inner"><span class="text-4xl">⭐</span><div class="text-[10px] font-bold text-rose-500 mt-2 uppercase">스타 x999</div></div></div>
                    <div class="p-4 bg-white"><button onclick="document.getElementById('reward-pop').style.display='none'" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-bold py-3 rounded-lg text-sm border-b-4 border-blue-900 transition-all active:border-b-0 active:translate-y-1">확인</button></div>
                </div>
            </div>

            <div id="item-info-pop" class="absolute inset-0 bg-black/40 backdrop-blur-sm hidden items-center justify-center p-6 z-[110]">
                <div class="bg-white rounded-xl w-full overflow-hidden border border-slate-300 shadow-2xl relative flex flex-col items-center">
                    <div class="w-full py-3 text-center text-slate-800 font-black text-[14px] border-b border-slate-200 bg-gray-50 relative">
                        아이템 상세 정보
                        <button onclick="document.getElementById('item-info-pop').style.display='none'" class="absolute right-4 top-1/2 -translate-y-1/2 text-slate-400 hover:text-slate-600 text-lg leading-none">✕</button>
                    </div>
                    <div class="p-6 w-full flex flex-col items-center bg-white gap-4">
                        <div class="w-24 h-28 bg-gradient-to-b from-[#22D3EE] to-[#1E3A8A] rounded border border-slate-200 flex flex-col items-center justify-center relative shadow-lg">
                            <i class="fa-solid fa-user text-white/50 text-4xl"></i>
                            <div class="absolute bottom-0 w-full bg-black/60 text-[10px] text-white text-center py-0.5">아이템 코드</div>
                        </div>
                        <div class="w-full text-center pl-1 pr-1">
                            <div class="text-slate-800 font-bold text-base mb-1.5 break-all">아이템 코드</div>
                            <div class="text-slate-500 text-xs leading-relaxed break-all">선수 강화 시, 사용되는 특별한 재료 아이템입니다.</div>
                        </div>
                        <div class="w-full bg-slate-50 rounded py-2 text-center mt-1 border border-slate-200">
                            <span class="text-slate-500 text-[11px] mr-1">현재 보유 수량:</span> 
                            <span class="text-yellow-600 font-black text-xs">999 개</span>
                        </div>
                    </div>
                    <div class="w-full p-4 bg-gray-50 flex justify-center items-center border-t border-slate-200 relative">
                        <button class="absolute left-4 bg-slate-200 hover:bg-slate-300 text-slate-600 font-bold py-1.5 px-3 rounded text-[10px] transition-colors shadow-sm active:translate-y-0.5">확률 정보</button>
                        <button onclick="document.getElementById('item-info-pop').style.display='none'" class="px-10 py-2.5 bg-[#d35400] text-white font-bold rounded-lg text-sm border-b-4 border-[#a04000] transition-all hover:bg-[#b24700] active:translate-y-1 active:border-b-0">닫기</button>
                    </div>
                </div>
            </div>

            <div id="stage-info-pop" class="absolute inset-0 bg-black/40 backdrop-blur-sm hidden items-center justify-center p-6 z-[110]">
                <div class="bg-white rounded-xl w-full overflow-hidden border border-slate-300 shadow-2xl relative flex flex-col items-center">
                    <div class="w-full py-3 text-center text-slate-800 font-black text-[14px] border-b border-slate-200 bg-gray-50 relative">
                        진행 단계별 보상 정보
                        <button onclick="document.getElementById('stage-info-pop').style.display='none'" class="absolute right-4 top-1/2 -translate-y-1/2 text-slate-400 hover:text-slate-600 text-lg leading-none">✕</button>
                    </div>
                    <div class="p-4 w-full flex flex-col gap-2 bg-white max-h-[350px] overflow-y-auto no-scrollbar">
                        <div class="flex items-center justify-between bg-slate-50 p-2.5 rounded-lg border border-slate-200 shadow-sm">
                            <span class="font-black text-slate-700 text-xs w-10 text-center">1단계</span>
                            <div class="flex-1 flex justify-center">
                                <div class="w-7 h-9 bg-gradient-to-b from-blue-400 to-blue-900 rounded-sm border border-slate-400 flex items-center justify-center shadow-sm">
                                    <i class="fa-solid fa-user text-white/50 text-[10px]"></i>
                                </div>
                            </div>
                            <span class="font-bold text-slate-800 text-xs w-12 text-right">x999</span>
                        </div>
                        <div class="flex items-center justify-between bg-slate-50 p-2.5 rounded-lg border border-slate-200 shadow-sm">
                            <span class="font-black text-slate-700 text-xs w-10 text-center">2단계</span>
                            <div class="flex-1 flex justify-center">
                                <div class="w-7 h-9 bg-gradient-to-b from-blue-400 to-blue-900 rounded-sm border border-slate-400 flex items-center justify-center shadow-sm">
                                    <i class="fa-solid fa-user text-white/50 text-[10px]"></i>
                                </div>
                            </div>
                            <span class="font-bold text-slate-800 text-xs w-12 text-right">x999</span>
                        </div>
                        <div class="flex items-center justify-between bg-slate-50 p-2.5 rounded-lg border border-slate-200 shadow-sm">
                            <span class="font-black text-slate-700 text-xs w-10 text-center">3단계</span>
                            <div class="flex-1 flex justify-center">
                                <div class="w-7 h-9 bg-gradient-to-b from-blue-400 to-blue-900 rounded-sm border border-slate-400 flex items-center justify-center shadow-sm">
                                    <i class="fa-solid fa-user text-white/50 text-[10px]"></i>
                                </div>
                            </div>
                            <span class="font-bold text-slate-800 text-xs w-12 text-right">x999</span>
                        </div>
                        <div class="flex items-center justify-between bg-slate-50 p-2.5 rounded-lg border border-slate-200 shadow-sm">
                            <span class="font-black text-slate-700 text-xs w-10 text-center">4단계</span>
                            <div class="flex-1 flex justify-center">
                                <div class="w-7 h-9 bg-gradient-to-b from-blue-400 to-blue-900 rounded-sm border border-slate-400 flex items-center justify-center shadow-sm">
                                    <i class="fa-solid fa-user text-white/50 text-[10px]"></i>
                                </div>
                            </div>
                            <span class="font-bold text-slate-800 text-xs w-12 text-right">x999</span>
                        </div>
                        <div class="flex items-center justify-between bg-slate-50 p-2.5 rounded-lg border border-slate-200 shadow-sm">
                            <span class="font-black text-slate-700 text-xs w-10 text-center">5단계</span>
                            <div class="flex-1 flex justify-center">
                                <div class="w-7 h-9 bg-gradient-to-b from-blue-400 to-blue-900 rounded-sm border border-slate-400 flex items-center justify-center shadow-sm">
                                    <i class="fa-solid fa-user text-white/50 text-[10px]"></i>
                                </div>
                            </div>
                            <span class="font-bold text-slate-800 text-xs w-12 text-right">x999</span>
                        </div>
                    </div>
                    <div class="w-full p-4 bg-gray-50 flex justify-center border-t border-slate-200">
                        <button onclick="document.getElementById('stage-info-pop').style.display='none'" class="px-10 py-2 bg-[#d35400] text-white font-bold rounded-lg text-sm border-b-4 border-[#a04000] active:border-b-0 active:translate-y-1 transition-all">확인</button>
                    </div>
                </div>
            </div>

        </div>

    </div>

    <script>
        function switchCategory(catId) {
            document.querySelectorAll('.achieve-cat').forEach(tab => tab.classList.remove('active'));
            document.querySelectorAll('.achieve-list-container').forEach(list => list.classList.remove('active'));
            document.getElementById('tab-' + catId).classList.add('active');
            document.getElementById('list-' + catId).classList.add('active');
        }

        // 전체 받기 팝업 
        function receiveAll() {
            document.getElementById('reward-pop').style.display = 'flex';
        }

        // 개별 아이템 상세 정보 팝업
        function showItemInfo() {
            document.getElementById('item-info-pop').style.display = 'flex';
        }

        // 진행 단계별 보상 정보 팝업
        function showStageRewardInfo() {
            document.getElementById('stage-info-pop').style.display = 'flex';
        }

        // 16글자 내용 (두 줄 고정)
        const achieveText = "일이삼사오육칠팔구십일이삼사오육<br>일이삼사오육칠팔구십일이삼사오육";

        window.onload = () => {
            const categories = ['list-tutorial', 'list-play', 'list-growth', 'list-etc'];
            const stagePattern = ['completed99', 2, 3, 4, 'completed5', 1]; 
            
            categories.forEach(catId => {
                const container = document.getElementById(catId);
                if (!container) return; 
                let content = "";
                
                for(let i = 0; i < 6; i++) {
                    let stage = stagePattern[i];
                    let isCompleted = (stage === 'completed99' || stage === 'completed5');
                    
                    // w-[100px] 로 축소하여 텍스트 우측에 겹치지 않게 배치
                    let topUI = "";
                    if (isCompleted) {
                        topUI = `<button onclick="document.getElementById('reward-pop').style.display='flex'" class="w-[100px] h-[28px] shrink-0 bg-gradient-to-b from-yellow-300 to-yellow-500 text-slate-800 font-bold rounded text-[12px] border-b-2 border-yellow-600 shadow-sm active:translate-y-0.5 active:border-b-0 ml-auto">받기</button>`;
                    } else {
                        let percent = (stage / 5) * 100;
                        topUI = `
                        <div class="w-[100px] bg-[#1e293b] h-3.5 relative border border-slate-400 flex items-center shadow-inner shrink-0 ml-auto">
                            <div class="bg-cyan-progress h-full" style="width: ${percent}%;"></div>
                            <span class="absolute w-full text-center text-[9px] font-bold text-white tracking-widest text-shadow">${stage}/5</span>
                        </div>`;
                    }

                    let bottomNodes = "";
                    let bottomText = "";
                    let linePercent = 0;
                    let bgGradientClass = isCompleted ? "bg-cyan-yellow-progress shadow-[0_0_5px_rgba(6,182,212,0.5)]" : "bg-cyan-progress shadow-[0_0_5px_cyan]";

                    if (stage === 'completed99') {
                        bottomText = "99/99";
                        linePercent = 100;
                        bottomNodes = `
                            <div class="w-3.5 h-3.5 rounded-full z-10 flex justify-center items-end relative border-2 bg-[#0f172a] border-cyan-400 shadow-[0_0_8px_cyan]">
                                <span class="absolute -bottom-3 text-[8px] text-slate-300 font-black">0</span>
                            </div>
                            <div class="w-3.5 h-3.5 rounded-full z-10 flex justify-center items-end relative border-2 bg-[#0f172a] border-yellow-400 shadow-[0_0_8px_yellow]">
                                <span class="absolute -bottom-3 text-[8px] text-slate-300 font-black">1</span>
                            </div>
                        `;
                    } else {
                        let currentStage = stage === 'completed5' ? 5 : stage;
                        bottomText = currentStage + "/5";
                        if (stage === 'completed5') bottomText = "5/5";
                        linePercent = ((currentStage - 1) / 4) * 100;
                        
                        let nodesArray = [];
                        for(let n=1; n<=5; n++) {
                            let activeNode = n <= currentStage;
                            let nodeClass = activeNode ? "bg-[#0f172a] border-cyan-400 shadow-[0_0_5px_cyan]" : "bg-[#475569] border-[#334155]";
                            if (stage === 'completed5' && n === 5) {
                                nodeClass = "bg-[#0f172a] border-yellow-400 shadow-[0_0_8px_yellow]";
                            }
                            nodesArray.push(`
                            <div class="w-3.5 h-3.5 rounded-full z-10 flex justify-center items-end relative border-2 ${nodeClass}">
                                <span class="absolute -bottom-3 text-[8px] text-slate-300 font-black">${n}</span>
                            </div>
                            `);
                        }
                        bottomNodes = nodesArray.join('');
                    }

                    // 리스트 아이템 UI 렌더링
                    content += `
                    <div class="bg-[#cbd5e1] border-b border-slate-400 shrink-0 shadow-sm relative">
                        <div class="flex bg-white h-[56px]">
                            <div class="w-[60px] bg-[#e11d48] skew-bg flex items-center justify-center relative z-10 shadow-[2px_0_5px_rgba(0,0,0,0.1)] shrink-0">
                                <i class="fa-solid fa-trophy text-white text-[22px] drop-shadow-md mr-2"></i>
                            </div>
                            <div class="flex-1 pl-1 pr-2 py-1 flex items-center justify-between relative -left-1 gap-1 overflow-hidden">
                                <span class="text-[9px] font-bold text-slate-800 leading-tight tracking-tighter whitespace-nowrap block w-[130px] break-all">${achieveText}</span>
                                ${topUI}
                            </div>
                            <div class="w-[65px] bg-[#1e293b] flex items-center justify-between px-1.5 border-l-2 border-slate-400 shrink-0">
                                <div onclick="showItemInfo()" class="w-6 h-8 bg-gradient-to-b from-blue-400 to-blue-900 rounded-sm border border-slate-400 flex flex-col items-center justify-center relative overflow-hidden shadow-md cursor-pointer hover:ring-1 hover:ring-white transition-all active:scale-95">
                                    <i class="fa-solid fa-user text-white/50 text-xs"></i>
                                    <div class="absolute bottom-0 w-full bg-black/60 text-[5px] text-white text-center py-[1px]">아이템</div>
                                </div>
                                <span class="text-white font-bold text-[11px]">999</span>
                            </div>
                        </div>
                        
                        <div class="h-9 relative flex items-center justify-between px-3 bg-[#475569] cursor-pointer hover:bg-[#334155] transition-colors" onclick="showStageRewardInfo()">
                            <div class="flex-[0.85] relative w-full flex justify-between items-center ${stage==='completed99'?'px-3':'pl-1 pr-6'} gap-0.5 pointer-events-none">
                                <div class="absolute left-4 right-${stage==='completed99'?'4':'8'} h-1.5 bg-[#334155] rounded-full z-0"></div>
                                <div class="absolute left-4 h-1.5 ${bgGradientClass} rounded-full z-0" style="width: calc((100% - ${stage==='completed99'?'32px':'48px'}) * ${linePercent / 100});"></div>
                                ${bottomNodes}
                            </div>
                            <span class="text-[11px] font-black text-slate-100 z-10 text-right w-12 bg-transparent pl-2 pointer-events-none">${bottomText}</span>
                        </div>
                    </div>
                    `;
                }
                container.innerHTML = content;
            });
        };
    </script>
</body>
</html>
