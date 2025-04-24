local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

-- Garantir que o Fluent foi carregado corretamente
if not Fluent then
    warn("Falha ao carregar o Fluent.")
    return
end

-- Criar a janela principal com o título alterado
local Window = Fluent:CreateWindow({
    Title = "CH-dead rils-CH",
    TabWidth = 120,
    Size = UDim2.fromOffset(480, 360),
    Theme = "Dark"
})

-- Criar as Tabs
local Tabs = {
    Classes = Window:AddTab({ Title = "Classes" }),
    ScriptsDeTPs = Window:AddTab({ Title = "Scripts" }),
    Creditos = Window:AddTab({ Title = "Créditos" })  -- Nome atualizado
}

-- CLASSES
local classes = {
    "Horse", "Musician", "Doctor", "Miner", "Arsonist", "Packmaster",
    "Necromancer", "Werewolf", "High Roller", "Conductor", "Cowboy",
    "The Alamo", "Zombie", "Vampire", "Priest", "Survivalist", "Ironclad"
}

for _, class in ipairs(classes) do
    Tabs.Classes:AddButton({
        Title = "Comprar " .. class,
        Callback = function()
            game:GetService("ReplicatedStorage")
                :WaitForChild("Shared")
                :WaitForChild("RemotePromise")
                :WaitForChild("Remotes")
                :WaitForChild("C_BuyClass"):FireServer(class)
        end
    })
end

-- Adicionar o botão "Skull hub" na aba Scripts
Tabs.ScriptsDeTPs:AddButton({
    Title = "Skull hub",
    Callback = function()
        loadstring(game:HttpGet('https://skullhub.xyz/loader.lua'))()
    end
})

-- Adicionar o botão "Null Fire" na aba Scripts
Tabs.ScriptsDeTPs:AddButton({
    Title = "Null Fire",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/InfernusScripts/Null-Fire/main/Loader"))()
    end
})

-- Adicionar o botão "Vfly (Cavalo)" na aba Scripts
Tabs.ScriptsDeTPs:AddButton({
    Title = "Vfly (Cavalo)",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/GhostPlayer352/Test4/main/Vehicle%20Fly%20Gui'))()
    end
})

-- Adicionar o botão "TP (Estruturas)" na aba Scripts
Tabs.ScriptsDeTPs:AddButton({
    Title = "TP (Estruturas)",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/ringtaa/deadrailsui.github.io/refs/heads/main/RINGTA.LUA"))()
    end
})

-- Adicionar o botão "Auto Bond" na aba Scripts
Tabs.ScriptsDeTPs:AddButton({
    Title = "Auto Bond",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/thiennrb7/Script/refs/heads/main/autobond"))()
    end
})

-- ESP com distância
local espEnabled = false
local espGuiTable = {}
local runService = game:GetService("RunService")
local localPlayer = game.Players.LocalPlayer

function createESP(player)
    if not player.Character or not player.Character:FindFirstChild("Head") then return end

    local billboard = Instance.new("BillboardGui")
    billboard.Name = "ESPGui"
    billboard.Adornee = player.Character.Head
    billboard.Size = UDim2.new(0, 100, 0, 40)
    billboard.AlwaysOnTop = true

    local text = Instance.new("TextLabel")
    text.Size = UDim2.new(1, 0, 1, 0)
    text.BackgroundTransparency = 1
    text.TextColor3 = Color3.fromRGB(255, 0, 0)
    text.TextStrokeTransparency = 0.5
    text.TextScaled = true
    text.Font = Enum.Font.SourceSansBold
    text.Text = player.Name

    text.Parent = billboard
    billboard.Parent = player.Character.Head
    espGuiTable[player] = { gui = billboard, label = text }
end

function removeESP(player)
    if espGuiTable[player] then
        espGuiTable[player].gui:Destroy()
        espGuiTable[player] = nil
    end
end

function updateDistances()
    if not espEnabled then return end
    for player, data in pairs(espGuiTable) do
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local dist = (localPlayer.Character.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
            data.label.Text = player.Name .. " [" .. math.floor(dist) .. "m]"
        end
    end
end

function toggleESP(state)
    espEnabled = state
    for _, player in ipairs(game.Players:GetPlayers()) do
        if player ~= localPlayer then
            if state then
                createESP(player)
            else
                removeESP(player)
            end
        end
    end
end

game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        wait(1)
        if espEnabled then createESP(player) end
    end)
end)

runService.RenderStepped:Connect(updateDistances)

Tabs.ScriptsDeTPs:AddToggle("ESP_Distance_Toggle", {
    Title = "ESP com Distância",
    Default = false
}):OnChanged(toggleESP)

-- MONITORAMENTO DE VIDA
local targetHealth = 100

Tabs.ScriptsDeTPs:AddSlider("HealthLimitSlider", {
    Title = "Vida mínima antes de sair",
    Description = "Se sua vida ficar menor que isso, o jogo fecha.",
    Default = 100,
    Min = 1,
    Max = 100,
    Rounding = 0,
    Size = UDim2.fromOffset(300, 50),  -- Tamanho ajustado
    Callback = function(value)
        targetHealth = value
    end
})

Tabs.ScriptsDeTPs:AddButton({
    Title = "Ativar auto-sair se vida cair",
    Callback = function()
        local player = game.Players.LocalPlayer
        local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")

        if not humanoid then
            player.CharacterAdded:Wait()
            humanoid = player.Character:WaitForChild("Humanoid")
        end

        humanoid.HealthChanged:Connect(function(newHealth)
            if newHealth < targetHealth then
                game:Shutdown()
            end
        end)

        Fluent:Notify({
            Title = "Sistema de Vida",
            Content = "Monitorando... Se sua vida cair abaixo de " .. targetHealth .. ", você será desconectado.",
            Duration = 5
        })
    end
})

-- Créditos
Tabs.Creditos:AddParagraph({
    Title = "Criado por",
    Content = "Cezar"
})

Tabs.Creditos:AddParagraph({
    Title = "Suporte",
    Content = "cezarr0294"
})
