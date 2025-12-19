-- Configurações Globais
_G.Slapfarmsnow = {
    glove = "Dual",
    enabled = true
}
_G.SnowSlapfarm = 100
_G.Snoweffect = true

local auto_equipglove = "Dual"
local auto_servehop = true
local cooldown = 0.1

-- Função Anti-Kick Básica
local function disableAntiKick()
    local mt = getrawmetatable(game)
    setreadonly(mt, false)
    local old = mt.__namecall
    mt.__namecall = newcclosure(function(self, ...)
        local method = getnamecallmethod()
        if method == "Kick" or method == "kick" then
            return nil -- Bloqueia a tentativa de kick
        end
        return old(self, ...)
    end)
    setreadonly(mt, true)
end
disableAntiKick()

-- Função para Equipar Luva Automaticamente
task.spawn(function()
    while task.wait(1) do
        local lplr = game.Players.LocalPlayer
        if lplr.Character and not lplr.Character:FindFirstChild(auto_equipglove) then
            -- Evento padrão de equipar luva no Slap Battles
            game:GetService("ReplicatedStorage").GloveSelected:FireServer(auto_equipglove)
        end
    end
end)

-- Função de Teleporte e Farm (Track Random Player)
task.spawn(function()
    while _G.Slapfarmsnow.enabled do
        local players = game.Players:GetPlayers()
        local randomPlayer = players[math.random(1, #players)]
        
        if randomPlayer and randomPlayer ~= game.Players.LocalPlayer and randomPlayer.Character then
            local targetHRP = randomPlayer.Character:FindFirstChild("HumanoidRootPart")
            local myHRP = game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
            
            if targetHRP and myHRP then
                -- Teleporte para o jogador aleatório
                myHRP.CFrame = targetHRP.CFrame * CFrame.new(0, 0, 3)
                
                -- Simulação de Slap (Uso do Item/Luva com Cooldown de 0.1)
                local event = game:GetService("ReplicatedStorage"):FindFirstChild("GeneralAbility") or game:GetService("ReplicatedStorage"):FindFirstChild("Slap")
                if event then
                    event:FireServer(randomPlayer.Character)
                end
            end
        end
        task.wait(cooldown) -- Item cooldown 0.1
    end
end)

-- Server Hop (Trocar de servidor após 1 segundo se ativado)
if auto_servehop then
    task.delay(1, function()
        local HttpService = game:GetService("HttpService")
        local TeleportService = game:GetService("TeleportService")
        local Servers = HttpService:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?sortOrder=Asc&limit=100"))
        
        for _, s in pairs(Servers.data) do
            if s.playing < s.maxPlayers and s.id ~= game.JobId then
                TeleportService:TeleportToPlaceInstance(game.PlaceId, s.id)
                break
            end
        end
    end)
end

print("Script Carregado no Codex: Farm Dual Ativado")

