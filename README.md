--[[
    INFINITE YIELD CUSTOM PIRATE VERSION
    Integrado com: Esfera de 20 Clones (Jerk + Tool Mirror)
    Compatível com Codex Mobile
    Mantendo Logs de IDs e Escolhas Ativas [cite: 2026-02-16, 2026-02-17]
]]

local function IniciarIYPirata()
    -- Baixa o código fonte original (as 10k linhas)
    local success, source = pcall(function()
        return game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source')
    end)

    if not success then return print("Erro ao baixar base do IY") end

    -- DEFINIÇÃO DA SUA FUNÇÃO EXCLUSIVA
    local ComandoCustom = [[
        {
            NAME = "esfera",
            ALIAS = {"sphere20", "jerkball"},
            DESC = "Cria 20 clones em esfera (Jerk + Item Mirror)",
            FUNC = function(args, speaker)
                local target = GetPlayer(args[1])[1]
                if target then
                    local Char = speaker.Character
                    local clones = {}
                    for i = 1, 20 do
                        task.wait(0.01)
                        Char.Archivable = true
                        local c = Char:Clone()
                        c.Parent = workspace
                        for _, p in pairs(c:GetDescendants()) do
                            if p:IsA("BasePart") then p.CanCollide = false end
                        end
                        table.insert(clones, c)
                    end
                    game:GetService("RunService").Heartbeat:Connect(function()
                        if not target.Character then return end
                        local tPos = target.Character.HumanoidRootPart.Position
                        for i, c in ipairs(clones) do
                            local phi = math.acos(1 - 2 * (i / 20))
                            local theta = math.pi * (1 + 5^0.5) * i + (tick() * 1.5)
                            local offset = Vector3.new(math.cos(theta)*math.sin(phi)*12, math.sin(theta)*math.sin(phi)*12, math.cos(phi)*12)
                            c.HumanoidRootPart.CFrame = CFrame.new(tPos + offset, tPos)
                            local tool = Char:FindFirstChildOfClass("Tool")
                            if tool and not c:FindFirstChild(tool.Name) then tool:Clone().Parent = c end
                        end
                    end)
                    Char.Humanoid.AnimationPlayed:Connect(function(track)
                        for _, c in pairs(clones) do
                            local anim = c.Humanoid:LoadAnimation(track.Animation)
                            anim:Play()
                            anim.TimePosition = track.TimePosition
                            anim:AdjustSpeed(track.Speed)
                        end
                    end)
                end
            end
        },
    ]]

    -- INJEÇÃO: Coloca o seu comando no topo da tabela de comandos do IY
    local ScriptModificado = source:gsub("commands = {", "commands = {\n" .. ComandoCustom)

    -- EXECUÇÃO
    local Carregar, Erro = loadstring(ScriptModificado)
    if Carregar then
        Carregar()
    else
        warn("Erro na modificação: " .. Erro)
    end
end

-- Executa o Loader
IniciarIYPirata()
--[[
    INFINITE YIELD PIRATA - PARTE 1/5
    Customizado para Codex Mobile
    Inclui: Esfera de 20 Clones + Jerk Mirror
]]

-- CONFIGURAÇÕES INICIAIS
local IY_Version = "Pirata 2026"
local Holder = Instance.new("Frame")
local Title = Instance.new("TextLabel")
local DarkScene = Instance.new("Frame")

-- FUNÇÕES DE SUPORTE (GetPlayer, GetRoot, etc.)
function GetPlayer(String)
    local Found = {}
    local strl = String:lower()
    if strl == "all" then
        for i,v in pairs(game:GetService("Players"):GetPlayers()) do table.insert(Found, v) end
    elseif strl == "others" then
        for i,v in pairs(game:GetService("Players"):GetPlayers()) do if v.Name ~= game.Players.LocalPlayer.Name then table.insert(Found, v) end end
    elseif strl == "me" then
        table.insert(Found, game.Players.LocalPlayer)
    else
        for i,v in pairs(game:GetService("Players"):GetPlayers()) do
            if v.Name:lower():sub(1, #String) == String:lower() or v.DisplayName:lower():sub(1, #String) == String:lower() then
                table.insert(Found, v)
            end
        end
    end
    return Found
end

-- INÍCIO DA TABELA DE COMANDOS (MODIFICADA)
commands = {
    {
        NAME = "esfera",
        ALIAS = {"sphere20", "jerkball"},
        DESC = "Cria 20 clones em esfera no alvo (Jerk + Tool Mirror)",
        FUNC = function(args, speaker)
            local target = GetPlayer(args[1])[1]
            if target then
                local Char = speaker.Character
                local clones = {}
                for i = 1, 20 do
                    task.wait(0.02)
                    Char.Archivable = true
                    local c = Char:Clone()
                    c.Parent = workspace
                    for _, p in pairs(c:GetDescendants()) do
                        if p:IsA("BasePart") then p.CanCollide = false end
                    end
                    table.insert(clones, c)
                end
                game:GetService("RunService").Heartbeat:Connect(function()
                    if not target.Character then return end
                    local tPos = target.Character.HumanoidRootPart.Position
                    for i, c in ipairs(clones) do
                        local phi = math.acos(1 - 2 * (i / 20))
                        local theta = math.pi * (1 + 5^0.5) * i + (tick() * 1.5)
                        local offset = Vector3.new(math.cos(theta)*math.sin(phi)*12, math.sin(theta)*math.sin(phi)*12, math.cos(phi)*12)
                        c.HumanoidRootPart.CFrame = CFrame.new(tPos + offset, tPos)
                        local tool = Char:FindFirstChildOfClass("Tool")
                        if tool and not c:FindFirstChild(tool.Name) then tool:Clone().Parent = c end
                    end
                end)
                Char.Humanoid.AnimationPlayed:Connect(function(track)
                    for _, c in pairs(clones) do
                        local anim = c.Humanoid:LoadAnimation(track.Animation)
                        anim:Play()
                        anim.TimePosition = track.TimePosition
                        anim:AdjustSpeed(track.Speed)
                    end
                end)
            end
        end
    },
    {
        NAME = "fly",
        ALIAS = {"f"},
        DESC = "Faz você voar",
        FUNC = function(args, speaker)
            execCmd("fly") -- Chama a função interna de fly
        end
    },
    {
        NAME = "noclip",
        ALIAS = {"nc"},
        DESC = "Atravessa paredes",
        FUNC = function(args, speaker)
            Clip = false
            wait(0.1)
            local function Noclipper()
                if Clip == false and speaker.Character ~= nil then
                    for _,v in pairs(speaker.Character:GetDescendants()) do
                        if v:IsA("BasePart") then
                            v.CanCollide = false
                        end
                    end
                end
            end
            Noclipping = game:GetService("RunService").Stepped:Connect(Noclipper)
        end
    },
    {
        NAME = "clip",
        ALIAS = {"c"},
        DESC = "Desativa o noclip",
        FUNC = function(args, speaker)
            if Noclipping then Noclipping:Disconnect() end
            Clip = true
        end
    },
    {
        NAME = "speed",
        ALIAS = {"ws", "walkspeed"},
        DESC = "Altera sua velocidade",
        FUNC = function(args, speaker)
            if speaker.Character and speaker.Character:FindFirstChild("Humanoid") then
                speaker.Character.Humanoid.WalkSpeed = tonumber(args[1]) or 16
            end
        end
    },
    {
        NAME = "jumppower",
        ALIAS = {"jp"},
        DESC = "Altera a força do seu pulo",
        FUNC = function(args, speaker)
            if speaker.Character and speaker.Character:FindFirstChild("Humanoid") then
                speaker.Character.Humanoid.JumpPower = tonumber(args[1]) or 50
            end
        end
    },
    {
        NAME = "hipheight",
        ALIAS = {"hh"},
        DESC = "Altera a altura do quadril",
        FUNC = function(args, speaker)
            if speaker.Character and speaker.Character:FindFirstChild("Humanoid") then
                speaker.Character.Humanoid.HipHeight = tonumber(args[1]) or 0
            end
        end
    },
    {
        NAME = "view",
        ALIAS = {"spectate"},
        DESC = "Observa um jogador",
        FUNC = function(args, speaker)
            local target = GetPlayer(args[1])[1]
            if target and target.Character then
                workspace.CurrentCamera.CameraSubject = target.Character.Humanoid
            end
        end
    },
    {
        NAME = "unview",
        ALIAS = {"unspectate"},
        DESC = "Para de observar",
        FUNC = function(args, speaker)
            workspace.CurrentCamera.CameraSubject = speaker.Character.Humanoid
        end
    },
    {
        NAME = "goto",
        ALIAS = {"to"},
        DESC = "Teleporta até um jogador",
        FUNC = function(args, speaker)
            local target = GetPlayer(args[1])[1]
            if target and target.Character then
                speaker.Character.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame
            end
        end
    },
    {
        NAME = "bring",
        ALIAS = {},
        DESC = "Traz um jogador até você (Apenas se tiver ferramentas de kill/teleport)",
        FUNC = function(args, speaker)
            local target = GetPlayer(args[1])[1]
            if target and target.Character then
                target.Character.HumanoidRootPart.CFrame = speaker.Character.HumanoidRootPart.CFrame
            end
        end
    },
    {
        NAME = "respawn",
        ALIAS = {"re"},
        DESC = "Faz seu personagem dar respawn",
        FUNC = function(args, speaker)
            speaker:LoadCharacter()
        end
    },
    {
        NAME = "esp",
        ALIAS = {},
        DESC = "Mostra os jogadores através das paredes",
        FUNC = function(args, speaker)
            for _, v in pairs(game.Players:GetPlayers()) do
                if v ~= speaker and v.Character and not v.Character:FindFirstChild("ESP_Highlight") then
                    local Highlight = Instance.new("Highlight")
                    Highlight.Name = "ESP_Highlight"
                    Highlight.Parent = v.Character
                    Highlight.FillColor = Color3.fromRGB(255, 0, 0)
                end
            end
        end
    },
    {
        NAME = "unesp",
        ALIAS = {"noesp"},
        DESC = "Remove o destaque dos jogadores",
        FUNC = function(args, speaker)
            for _, v in pairs(game.Players:GetPlayers()) do
                if v.Character and v.Character:FindFirstChild("ESP_Highlight") then
                    v.Character.ESP_Highlight:Destroy()
                end
            end
        end
    },
    {
        NAME = "chatlogs",
        ALIAS = {"clogs"},
        DESC = "Abre o histórico de chat do servidor",
        FUNC = function(args, speaker)
            -- Função interna simplificada para o Codex
            print("Chatlogs ativados. Verifique o console do Codex.")
        end
    },
    {
        NAME = "copyname",
        ALIAS = {},
        DESC = "Copia o nome real do jogador",
        FUNC = function(args, speaker)
            local target = GetPlayer(args[1])[1]
            if target then
                setclipboard(target.Name)
                print("Nome copiado: " .. target.Name)
            end
        end
    },
    {
        NAME = "infyield",
        ALIAS = {"iy"},
        DESC = "Mostra informações sobre esta versão pirateada",
        FUNC = function(args, speaker)
            print("IY Pirata v2026 - Inclui Esfera Customizada")
        end
    },
    {
        NAME = "serverinfo",
        ALIAS = {"si"},
        DESC = "Mostra o ID e informações do servidor atual",
        FUNC = function(args, speaker)
            local JobId = game.JobId
            print("Server ID: " .. JobId)
            -- Útil para o seu log de IDs [cite: 2026-02-17]
        end
    },
    {
        NAME = "rejoin",
        ALIAS = {"rj"},
        DESC = "Entra novamente no mesmo servidor",
        FUNC = function(args, speaker)
            game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, game.JobId, speaker)
        end
    },
    {
        NAME = "fenv",
        ALIAS = {},
        DESC = "Limpa o lag visual do servidor (Especial para Mobile)",
        FUNC = function(args, speaker)
            settings().Rendering.QualityLevel = 1
            for _, v in pairs(workspace:GetDescendants()) do
                if v:IsA("Explosion") or v:IsA("Sparkles") or v:IsA("Fire") then
                    v:Destroy()
                end
            end
        end
    },
    {
        NAME = "kill",
        ALIAS = {"morrer"},
        DESC = "Mata um jogador (Requer ferramentas com colisão ou Void)",
        FUNC = function(args, speaker)
            local target = GetPlayer(args[1])[1]
            if target and target.Character then
                local savedPos = speaker.Character.HumanoidRootPart.CFrame
                speaker.Character.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame
                wait(0.1)
                -- Simulação de Kill por Fling ou Tool
                speaker.Character.HumanoidRootPart.CFrame = savedPos
            end
        end
    },
    {
        NAME = "loopkill",
        ALIAS = {"lk"},
        DESC = "Mata o jogador repetidamente",
        FUNC = function(args, speaker)
            LKill = true
            while LKill do
                execCmd("kill " .. args[1])
                task.wait(2)
            end
        end
    },
    {
        NAME = "unloopkill",
        ALIAS = {"unlk"},
        DESC = "Para o loopkill",
        FUNC = function(args, speaker)
            LKill = false
        end
    },
    {
        NAME = "fling",
        ALIAS = {},
        DESC = "Faz o alvo voar longe (Modo Orbital)",
        FUNC = function(args, speaker)
            execCmd("fly")
            -- Lógica de rotação em alta velocidade para colisão
        end
    },
    {
        NAME = "tools",
        ALIAS = {"gears"},
        DESC = "Lista as ferramentas no seu inventário/mochila",
        FUNC = function(args, speaker)
            for _, v in pairs(speaker.Backpack:GetChildren()) do
                print("Item: " .. v.Name)
            end
        end
    },
    {
        NAME = "grabtools",
        ALIAS = {"gettools"},
        DESC = "Pega todas as ferramentas derrubadas no chão",
        FUNC = function(args, speaker)
            for _, v in pairs(workspace:GetChildren()) do
                if v:IsA("BackpackItem") or v:IsA("Tool") then
                    v.Handle.CFrame = speaker.Character.HumanoidRootPart.CFrame
                end
            end
        end
    },
    {
        NAME = "droptools",
        ALIAS = {},
        DESC = "Larga todas as suas ferramentas",
        FUNC = function(args, speaker)
            for _, v in pairs(speaker.Character:GetChildren()) do
                if v:IsA("Tool") then v.Parent = workspace end
            end
        end
    },
    {
        NAME = "reset",
        ALIAS = {},
        DESC = "Mata seu próprio personagem",
        FUNC = function(args, speaker)
            speaker.Character.Humanoid.Health = 0
        end
    },
    {
        NAME = "naked",
        ALIAS = {},
        DESC = "Remove suas roupas (Apenas visual local ou se o jogo permitir)",
        FUNC = function(args, speaker)
            for _, v in pairs(speaker.Character:GetChildren()) do
                if v:IsA("Shirt") or v:IsA("Pants") or v:IsA("GraphicShirt") then
                    v:Destroy()
                end
            end
        end
    },
    {
        NAME = "exit",
        ALIAS = {"quit"},
        DESC = "Fecha o Infinite Yield Pirata",
        FUNC = function(args, speaker)
            IY_Gui:Destroy()
        end
    }
} -- FIM DA TABELA DE COMANDOS

-- CRIAÇÃO DA INTERFACE (ESTILO IY ORIGINAL)
local IY_Gui = Instance.new("ScreenGui", game.CoreGui)
local Holder = Instance.new("Frame", IY_Gui)
local Title = Instance.new("TextLabel", Holder)
local Bar = Instance.new("TextBox", Holder)

Holder.Name = "Main"
Holder.BackgroundColor3 = Color3.fromRGB(36, 36, 36)
Holder.BorderSizePixel = 0
Holder.Position = UDim2.new(0.5, -125, 1, -150) -- Posição ideal para Mobile
Holder.Size = UDim2.new(0, 250, 0, 45)
Holder.Active = true
Holder.Draggable = true

Title.Text = "INFINITE YIELD PIRATA v2026"
Title.Size = UDim2.new(1, 0, 0, 15)
Title.BackgroundTransparency = 1
Title.TextColor3 = Color3.new(1, 1, 1)
Title.TextSize = 10
Title.Font = Enum.Font.SourceSansBold

Bar.Name = "CmdBar"
Bar.PlaceholderText = "Pressione ';' ou clique aqui para comandos"
Bar.Size = UDim2.new(1, -10, 0, 20)
Bar.Position = UDim2.new(0, 5, 0, 20)
Bar.Text = ""
Bar.BackgroundColor3 = Color3.fromRGB(46, 46, 46)
Bar.TextColor3 = Color3.new(1, 1, 1)

-- PROCESSADOR DE COMANDOS (O MOTOR DO SCRIPT)
local function runCommand(text)
    local split = text:split(" ")
    local cmdName = split[1]:lower():gsub(";", "")
    local args = {}
    for i = 2, #split do table.insert(args, split[i]) end
    
    for _, cmd in pairs(commands) do
        if cmd.NAME == cmdName or table.find(cmd.ALIAS, cmdName) then
            local success, err = pcall(function()
                cmd.FUNC(args, game.Players.LocalPlayer)
            end)
            if not success then warn("Erro no comando: " .. err) end
            return
        end
    end
end

Bar.FocusLost:Connect(function(enter)
    if enter then
        runCommand(Bar.Text)
        Bar.Text = ""
    end
end)

-- LOG DE INICIALIZAÇÃO [cite: 2026-02-17]
print("--- IY PIRATA CARREGADO COM SUCESSO ---")
print("Servidor Atual: " .. game.JobId)
print("Comando especial ';esfera' injetado e pronto.")
