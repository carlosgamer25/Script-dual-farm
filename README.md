-- IY PIRATA LITE (ANTI-CRASH)
-- Focado em Performance para Codex e Build A Boat
-- [cite: 2026-02-16, 2026-02-17]

local Player = game.Players.LocalPlayer
local Char = Player.Character
local Clones = {}

-- Interface compacta para não ocupar RAM
local Gui = Instance.new("ScreenGui", game.CoreGui)
local Main = Instance.new("Frame", Gui)
Main.Size = UDim2.new(0, 200, 0, 40)
Main.Position = UDim2.new(0.5, -100, 0.8, 0)
Main.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Main.Active = true
Main.Draggable = true

local Bar = Instance.new("TextBox", Main)
Bar.Size = UDim2.new(1, -10, 1, -10)
Bar.Position = UDim2.new(0, 5, 0, 5)
Bar.PlaceholderText = ";esfera [nick] ou ;fly"
Bar.Text = ""
Bar.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
Bar.TextColor3 = Color3.new(1, 1, 1)

-- FUNÇÃO DA ESFERA (Sincronizada com seu Log de IDs)
local function AtivarEsfera(tName)
    local target = nil
    for _, v in pairs(game.Players:GetPlayers()) do
        if v.Name:lower():find(tName:lower()) then target = v break end
    end
    
    if target and target.Character then
        -- Limpa clones antigos antes de criar novos (Evita Crash)
        for _, v in pairs(Clones) do v:Destroy() end
        Clones = {}

        for i = 1, 20 do
            task.wait(0.02)
            Char.Archivable = true
            local c = Char:Clone()
            c.Parent = workspace
            -- Remove acessórios pesados para o Codex aguentar
            for _, acc in pairs(c:GetChildren()) do
                if acc:IsA("Accessory") or acc:IsA("Shirt") then acc:Destroy() end
            end
            table.insert(Clones, c)
        end

        game:GetService("RunService").Heartbeat:Connect(function()
            if not target.Character then return end
            local tPos = target.Character.HumanoidRootPart.Position
            for i, c in ipairs(Clones) do
                local phi = math.acos(1 - 2 * (i / 20))
                local theta = math.pi * (1 + 5^0.5) * i + (tick() * 1.5)
                c.HumanoidRootPart.CFrame = CFrame.new(tPos + Vector3.new(math.cos(theta)*math.sin(phi)*12, math.sin(theta)*math.sin(phi)*12, math.cos(phi)*12), tPos)
            end
        end)
    end
end

-- Processador de Comandos (As funções que você realmente usa)
Bar.FocusLost:Connect(function(enter)
    if enter then
        local msg = Bar.Text:lower()
        if msg:find(";esfera") then
            AtivarEsfera(msg:split(" ")[2])
        elseif msg == ";fly" then
            loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))() -- Só carrega o IY se você pedir
        elseif msg == ";re" then
            Player:LoadCharacter()
        end
        Bar.Text = ""
    end
end)
