-- AUTO ROUBAR INVENTÁRIO (FILTRADO POR LISTA)

Tabs.Main
:AddLeftGroupbox("Auto Roubar Inventário", "Boxes")
:AddToggle("Auto Roubar Inventário", {
    Text = "Auto Roubar Inventário",
    Callback = function(Value)

        local ReplicatedStorage = game:GetService("ReplicatedStorage")
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer

        --------------------------------------------------
        -- BLOQUEAR NOTIFY / NOTIFYCLIENT
        --------------------------------------------------
        if ReplicatedStorage:FindFirstChild("NotifyClient")
        and ReplicatedStorage.NotifyClient:IsA("BindableEvent") then
            for _, c in pairs(getconnections(ReplicatedStorage.NotifyClient.Event)) do
                if Value then c:Disable() else c:Enable() end
            end
        end

        if ReplicatedStorage:FindFirstChild("Notify")
        and ReplicatedStorage.Notify:IsA("RemoteEvent") then
            for _, c in pairs(getconnections(ReplicatedStorage.Notify.OnClientEvent)) do
                if Value then c:Disable() else c:Enable() end
            end
        end

        --------------------------------------------------
        -- DELETAR NotifyGui VISUAL
        --------------------------------------------------
        local function deletarNotifyGui()
            local pg = LocalPlayer:FindFirstChild("PlayerGui")
            if not pg then return end
            for _, gui in ipairs(pg:GetChildren()) do
                if gui:IsA("ScreenGui") and gui.Name == "NotifyGui" then
                    gui:Destroy()
                end
            end
        end

        --------------------------------------------------
        -- LISTA DE ITENS PERMITIDOS (PEGA TODOS ESSES)
        --------------------------------------------------
        local itensPermitidos = {
            ["Glock 17"] = true,
            ["Hi Power"] = true,
            ["AK47"] = true,
            ["AK47 Natalina"] = true,
            ["PARAFAL"] = true,
            ["Uzi"] = true,
            ["G3"] = true,
            ["IA2"] = true,
            ["AR-15"] = true,
            ["Faca"] = true,
            ["Natalina"] = true,
            ["Lockpick"] = true,
            ["Escudo"] = true,
            ["Skate"] = true,
            ["Planta Suja"] = true,
            ["Planta Limpa"] = true,
            ["Tratamento"] = true,
            ["Xbox"] = true,
            ["Energético"] = true,
            ["Desruptor"] = true,
            ["Dinamite"] = true,
            ["Peça de Arma"] = true,
            ["Armação de Arma"] = true,
            ["HK416"] = true,
            ["Saco de lixo"] = true,
            ["PS5"] = true,
            ["C4"] = true,
            ["USP"] = true,
            ["Plástico Vazio"] = true,
            ["Plástico Pronto"] = true,
            ["Presente Verde"] = true,
            ["Presente Vermelho"] = true,
            ["Presente Azul"] = true
        }

        --------------------------------------------------
        -- LOOP PRINCIPAL
        --------------------------------------------------
        if Value then
            task.spawn(function()
                while Library.Toggles["Auto Roubar Inventário"].Value do

                    deletarNotifyGui()

                    local gui = LocalPlayer:FindFirstChild("PlayerGui")
                    if gui
                    and gui:FindFirstChild("BackpackNova")
                    and gui.BackpackNova:FindFirstChild("Inventario")
                    and gui.BackpackNova.Inventario:FindFirstChild("Outro")
                    and gui.BackpackNova.Inventario.Outro:FindFirstChild("conteudo") then

                        for _, slot in pairs(gui.BackpackNova.Inventario.Outro.conteudo:GetChildren()) do
                            if slot:FindFirstChild("In") then
                                for _, item in pairs(slot.In:GetChildren()) do
                                    if item:FindFirstChild("Qnt")
                                    and itensPermitidos[item.Name] then

                                        ReplicatedStorage
                                        .Modules
                                        .InvRemotes
                                        .InvRequest
                                        :InvokeServer(
                                            "mudaInv",
                                            "3",
                                            item.Name,
                                            tonumber(item.Qnt.Text:sub(2))
                                        )

                                    end
                                end
                            end
                        end
                    end

                    task.wait(0.1)
                end
            end)
        end
    end
})
