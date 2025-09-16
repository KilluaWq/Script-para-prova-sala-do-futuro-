# Script-para-prova-sala-do-futuro-
Esse script e um para a prova Paulista 



                    ">âœ•</button>
                </div>
                <div style="padding: 25px; background: #000011;" id="input-section">
                    <textarea id="json-area" placeholder="Cole seu JSON aqui..." style="
                        width: 100%; height: 180px; padding: 16px;
                        background: #001133; color: #99ccff;
                        border: 1px solid rgba(0, 102, 255, 0.3);
                        border-radius: 8px; font-family: monospace;
                        resize: vertical; outline: none;
                        box-shadow: inset 0 1px 4px rgba(0,0,0,0.6);
                    "></textarea>
                    <button id="proc-btn" style="
                        margin-top: 18px; width: 100%; padding: 13px;
                        background: linear-gradient(135deg, #0066ff, #003366);
                        color: white; border: none; border-radius: 8px;
                        font-weight: 600; cursor: pointer;
                        box-shadow: 0 3px 15px rgba(0, 102, 255, 0.4);
                    ">ðŸŽ¯ Processar Gabarito</button>
                </div>
                <div id="result-section" style="padding: 0 25px 25px; display: none;">
                    <div id="result" style="
                        margin-top: 25px; padding: 20px;
                        background: #000000; border-radius: 8px;
                        color: #99ccff; min-height: 60px;
                        max-height: 500px; overflow-y: auto; line-height: 1.6;
                    "></div>
                </div>
                <div style="
                    padding: 12px 24px; background: #000; border-top: 1px solid #003366;
                    font-size: 12px; color: #666; text-align: center;
                ">
                    Feito por <a href="https://github.com/KilluaWq" target="_blank" style="color:#6699ff; text-decoration: none; font-weight: bold;">@bakai</a> & 
                    <a href="#" style="color:#6699ff; text-decoration: none; font-weight: bold;">@trampos</a> â€¢ 
                    <a href="https://discord.com/invite/kmeuwvXTNH" target="_blank" style="color:#00ccff; text-decoration: none;">Servidor Discord</a>
                </div>
            </div>
        `;
        panel.style.cssText = `
            position: fixed; top: 50%; left: 50%; transform: translate(-50%, -50%);
            z-index: 999998; pointer-events: none;
            background: rgba(0,0,0,0.9); display: flex;
            align-items: center; justify-content: center;
            opacity: 0; visibility: hidden;
            transition: opacity 0.35s, visibility 0.35s;
        `;

        document.body.appendChild(btn);
        document.body.appendChild(panel);

        // ========== DRAG & DROP ========== //
        const header = panel.querySelector("#infinity-header");
        let isDragging = false;
        let offsetX = 0, offsetY = 0;

        header.addEventListener("mousedown", (e) => {
            isDragging = true;
            const rect = panel.getBoundingClientRect();
            offsetX = e.clientX - rect.left;
            offsetY = e.clientY - rect.top;
            panel.style.transition = "none";
            e.preventDefault();
        });

        document.addEventListener("mousemove", (e) => {
            if (!isDragging) return;
            panel.style.pointerEvents = "none";
            const x = e.clientX - offsetX;
            const y = e.clientY - offsetY;
            panel.style.left = x + "px";
            panel.style.top = y + "px";
            panel.style.transform = "none";
        });

        document.addEventListener("mouseup", () => {
            isDragging = false;
            panel.style.transition = "opacity 0.35s, visibility 0.35s";
            panel.style.pointerEvents = "auto";
        });
        // ========== FIM DRAG & DROP ========== //

        // Processar JSON
        panel.querySelector("#proc-btn").onclick = () => {
            try {
                const input = panel.querySelector("#json-area").value;
                let data = JSON.parse(input);
                if (!Array.isArray(data)) data = [data];

                if (!data.length) throw new Error("JSON deve conter pelo menos uma questÃ£o.");

                const resultDiv = panel.querySelector("#result");

                // RENDERIZA O GABARITO â€” COM SUPORTE AO FORMATO REAL
                resultDiv.innerHTML = data.map(item => {
                    const letra = Object.keys(item.alternativa_correta)[0];
                    const texto = item.alternativa_correta[letra];

                    let html = `<div style="padding:18px 0; margin-bottom:22px;">`;

                    // â†’ ID
                    if (item.id_da_questao) {
                        html += `<div style="font-size:13px; color:#6699ff; margin-bottom:8px">ID: ${item.id_da_questao}</div>`;
                    }

                    // â†’ IMAGEM (se houver)
                    if (item.imagem_url) {
                        html += `<div style="margin: 12px 0; text-align: center;">
                            <img src="${item.imagem_url}" alt="Imagem da questÃ£o" style="max-width:100%; max-height:200px; border-radius:6px; border:1px solid rgba(102,153,255,0.2);">
                        </div>`;
                    }

                    // â†’ ENUNCIADO
                    const enunciado = item.enunciado || "[Enunciado nÃ£o definido]";
                    html += `
                        <div style="
                            font-weight: 700; margin: 10px 0 12px; color: #66ccff; font-size: 17px; line-height: 1.5;
                        ">${enunciado}</div>
                        <div style="
                            display: flex; align-items: center; gap: 10px; margin-top: 8px;
                        ">
                            <span style="
                                font-weight: 700; color: #6699ff;
                                background: rgba(102, 153, 255, 0.15); padding: 5px 12px;
                                border-radius: 6px; font-size: 15px;
                            ">${letra})</span>
                            <span>${texto}</span>
                        </div>
                    </div>`;

                    return html;
                }).join("");

                resultDiv.style.color = "#99ccff";

                // â†’ OCULTA INPUT
                panel.querySelector("#input-section").style.display = "none";
                panel.querySelector("#result-section").style.display = "block";

            } catch (err) {
                const resultDiv = panel.querySelector("#result");
                resultDiv.innerHTML = `<span style="color:#6699ff; font-weight:600">âŒ Erro: ${err.message || err}</span>`;
                panel.querySelector("#result-section").style.display = "block";
            }
        };

        // Toggle abrir/fechar
        btn.onclick = () => {
            const p = panel;
            if (p.style.opacity === "1") {
                p.style.opacity = "0";
                p.style.visibility = "hidden";
            } else {
                p.style.opacity = "1";
                p.style.visibility = "visible";
                p.style.left = "50%";
                p.style.top = "50%";
                p.style.transform = "translate(-50%, -50%)";
            }
        };

        // Fechar com Ã—
        panel.querySelector("#close-btn").onclick = () => {
            panel.style.opacity = "0";
            panel.style.visibility = "hidden";
        };

        // Fechar clicando fora
        panel.onclick = (e) => {
            if (e.target === panel) {
                panel.style.opacity = "0";
                panel.style.visibility = "hidden";
            }
        };
    }
})();
