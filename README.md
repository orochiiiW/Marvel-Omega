-- ESP Script para destacar objetos no mapa, teletransportar automaticamente e contorno verde

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local function teleportToItem(item)
    -- Teletransporta o jogador para a posição do item
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = item.CFrame + Vector3.new(0, 5, 0) -- Teleporta acima do item
    end
end

local function createESP(target)
    -- Verifica se o objeto já possui ESP
    if target:FindFirstChild("ESP") or target:FindFirstChild("Highlight") then return end

    -- Criação de um BillboardGui para mostrar o ESP (texto)
    local billboard = Instance.new("BillboardGui")
    billboard.Name = "ESP"
    billboard.Adornee = target
    billboard.Size = UDim2.new(20, 0, 10, 0) -- Dimensões ajustadas para o tamanho do texto
    billboard.AlwaysOnTop = true

    local textLabel = Instance.new("TextLabel", billboard)
    textLabel.Size = UDim2.new(1, 0, 1, 0)
    textLabel.BackgroundTransparency = 1
    textLabel.TextColor3 = Color3.new(1, 0, 0) -- Cor do texto (vermelho)
    textLabel.Text = target.Name
    textLabel.TextScaled = false
    textLabel.Font = Enum.Font.SourceSansBold
    textLabel.TextSize = 300 -- Tamanho ajustado para 300

    billboard.Parent = target

    -- Adiciona um contorno com a cor verde (Highlight)
    local highlight = Instance.new("Highlight")
    highlight.Name = "Highlight"
    highlight.Adornee = target
    highlight.FillTransparency = 1 -- Remove o preenchimento interno
    highlight.OutlineColor = Color3.new(0, 1, 0) -- Cor da borda (verde)
    highlight.OutlineTransparency = 0 -- Contorno totalmente visível
    highlight.Parent = target

    -- Teletransporta automaticamente assim que o objeto aparece
    teleportToItem(target)
end

-- Função para encontrar novos objetos
local function highlightItems()
    local itemsFolder = workspace:FindFirstChild("Items") -- Altere 'Items' para o nome da pasta de itens no mapa

    if itemsFolder then
        for _, item in pairs(itemsFolder:GetChildren()) do
            if item:IsA("BasePart") or item:IsA("Model") then
                createESP(item)
            end
        end
    end
end

-- Monitorando o workspace para novos spawns
workspace.ChildAdded:Connect(function(child)
    if child:IsA("BasePart") or child:IsA("Model") then
        createESP(child)
    end
end)

-- Inicializando o ESP nos itens existentes
highlightItems()
