--[[
Script para Arceus X - Roblox
Função: Cria um botão "tp" laranja, que abre uma GUI para escolher um player e teleportar até ele.

Como usar:
1. Execute este script no Arceus X.
2. Clique no botão laranja "tp" para abrir a GUI.
3. Selecione o player na lista e clique em "teleport" para se teleportar até ele.
]]

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Função para criar um botão
local function createButton(parent, name, text, color, position, size)
    local btn = Instance.new("TextButton")
    btn.Name = name
    btn.Text = text
    btn.BackgroundColor3 = color
    btn.Position = position
    btn.Size = size
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 18
    btn.Parent = parent
    return btn
end

-- GUI principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "TPGui"
screenGui.Parent = game.CoreGui

-- Botão "tp" laranja
local tpButton = createButton(
    screenGui,
    "TPButton",
    "tp",
    Color3.fromRGB(255,140,0), -- Laranja
    UDim2.new(0, 20, 0, 20),
    UDim2.new(0, 60, 0, 60)
)

-- GUI secundária (inicialmente invisível)
local frame = Instance.new("Frame")
frame.Name = "TeleportFrame"
frame.BackgroundColor3 = Color3.fromRGB(40,40,40)
frame.Position = UDim2.new(0.5, -110, 0.5, -110)
frame.Size = UDim2.new(0, 220, 0, 220)
frame.Visible = false
frame.Parent = screenGui

local title = Instance.new("TextLabel")
title.Text = "Selecione o Player"
title.Size = UDim2.new(1,0,0,30)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.new(1,1,1)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 20
title.Parent = frame

-- Scrolling list de players
local playerList = Instance.new("ScrollingFrame")
playerList.Name = "PlayerList"
playerList.Position = UDim2.new(0,10,0,40)
playerList.Size = UDim2.new(1,-20,0,100)
playerList.CanvasSize = UDim2.new(0,0,0,0)
playerList.BackgroundColor3 = Color3.fromRGB(30,30,30)
playerList.ScrollBarThickness = 6
playerList.Parent = frame

-- Botão "teleport" amarelo
local teleportButton = createButton(
    frame,
    "TeleportButton",
    "teleport",
    Color3.fromRGB(255, 215, 0), -- Amarelo
    UDim2.new(0.5, -50, 1, -60),
    UDim2.new(0, 100, 0, 40)
)
teleportButton.Active = false -- Só ativa quando um player é selecionado

-- Variável para guardar o player escolhido
local selectedPlayer = nil

-- Função para atualizar lista de players
local function updatePlayerList()
    playerList:ClearAllChildren()
    local y = 0
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            local btn = Instance.new("TextButton")
            btn.Size = UDim2.new(1,0,0,30)
            btn.Position = UDim2.new(0,0,0,y)
            btn.BackgroundColor3 = Color3.fromRGB(60,60,60)
            btn.Text = plr.Name
            btn.TextColor3 = Color3.new(1,1,1)
            btn.Font = Enum.Font.SourceSans
            btn.TextSize = 18
            btn.Parent = playerList
            btn.MouseButton1Click:Connect(function()
                selectedPlayer = plr
                teleportButton.Active = true
                -- Destaca botão selecionado
                for _, child in ipairs(playerList:GetChildren()) do
                    if child:IsA("TextButton") then
                        child.BackgroundColor3 = Color3.fromRGB(60,60,60)
                    end
                end
                btn.BackgroundColor3 = Color3.fromRGB(255,140,0)
            end)
            y = y + 32
        end
    end
    playerList.CanvasSize = UDim2.new(0,0,0,y)
end

-- Clique no botão "tp" mostra GUI
tpButton.MouseButton1Click:Connect(function()
    frame.Visible = true
    updatePlayerList()
end)

-- Clique no botão "teleport"
teleportButton.MouseButton1Click:Connect(function()
    if selectedPlayer and selectedPlayer.Character and selectedPlayer.Character:FindFirstChild("HumanoidRootPart") then
        local hrp = selectedPlayer.Character.HumanoidRootPart
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            LocalPlayer.Character.HumanoidRootPart.CFrame = hrp.CFrame + Vector3.new(2,0,0)
        end
        frame.Visible = false
        selectedPlayer = nil
        teleportButton.Active = false
    end
end)

-- Fecha GUI se clicar fora (opcional)
frame.Active = true
frame.Draggable = true

-- Atualiza lista de players em tempo real
Players.PlayerAdded:Connect(updatePlayerList)
Players.PlayerRemoving:Connect(updatePlayerList)

-- Pronto!
