--[[ 
    MM2 ULTRA FAKE TRADE + PREMIUM ANIME UI
    Compatível com: Xeno, Fluxus, Delta, Hydrogen
]]

local Library = {}
local TrackedItems = {}
local UI_Open = true

-- Configurações de Estilo
local Theme = {
    Main = Color3.fromRGB(20, 20, 25),
    Accent = Color3.fromRGB(180, 0, 255), -- Roxo Neon
    Text = Color3.fromRGB(255, 255, 255),
    Secondary = Color3.fromRGB(40, 40, 50),
    AnimeImg = "https://i.imgur.com/S8vS8vS.jpg" -- Imagem de fundo anime (exemplo)
}

-- Criando a UI
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local UICorner = Instance.new("UICorner")
local Title = Instance.new("TextLabel")
local StatusLabel = Instance.new("TextLabel")
local ToggleBtn = Instance.new("TextButton")
local BackgroundImg = Instance.new("ImageLabel")
local Shadow = Instance.new("ImageLabel")

-- Propriedades da UI
ScreenGui.Parent = game.CoreGui
ScreenGui.Name = "XenoFakeTradeUI"
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Theme.Main
MainFrame.Position = UDim2.new(0.35, 0, 0.3, 0)
MainFrame.Size = UDim2.new(0, 300, 0, 400)
MainFrame.ClipsDescendants = true

UICorner.CornerRadius = UDim.new(0, 20)
UICorner.Parent = MainFrame

BackgroundImg.Name = "Background"
BackgroundImg.Parent = MainFrame
BackgroundImg.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
BackgroundImg.BackgroundTransparency = 0.7
BackgroundImg.Position = UDim2.new(0, 0, 0, 0)
BackgroundImg.Size = UDim2.new(1, 0, 1, 0)
BackgroundImg.Image = Theme.AnimeImg
BackgroundImg.ZIndex = 0

Title.Name = "Title"
Title.Parent = MainFrame
Title.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 0, 0, 20)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Font = Enum.Font.GothamBold
Title.Text = "XENO MM2 CHEAT"
Title.TextColor3 = Theme.Text
Title.TextSize = 22
Title.ZIndex = 2

StatusLabel.Name = "Status"
StatusLabel.Parent = MainFrame
StatusLabel.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Position = UDim2.new(0, 0, 0, 60)
StatusLabel.Size = UDim2.new(1, 0, 0, 20)
StatusLabel.Font = Enum.Font.Gotham
StatusLabel.Text = "Status: ATIVADO ✅"
StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 150)
StatusLabel.TextSize = 14
StatusLabel.ZIndex = 2

ToggleBtn.Name = "ToggleBtn"
ToggleBtn.Parent = MainFrame
ToggleBtn.BackgroundColor3 = Theme.Accent
ToggleBtn.Position = UDim2.new(0.1, 0, 0.8, 0)
ToggleBtn.Size = UDim2.new(0, 220, 0, 50)
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.Text = "DESATIVAR SCRIPT"
ToggleBtn.TextColor3 = Theme.Text
ToggleBtn.TextSize = 16
ToggleBtn.ZIndex = 2

local BtnCorner = Instance.new("UICorner", ToggleBtn)
BtnCorner.CornerRadius = UDim.new(0, 12)

-- Animação de Entrada
MainFrame.Position = UDim2.new(0.35, 0, 1.2, 0)
game:GetService("TweenService"):Create(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Back), {Position = UDim2.new(0.35, 0, 0.3, 0)}):Play()

-- Lógica de Fake Trade
local function InjectFakeSlot(frame, itemData)
    local Template = frame:FindFirstChildWhichIsA("Frame")
    if not Template then return end

    local FakeSlot = Template:Clone()
    local Tag = Instance.new("BoolValue", FakeSlot)
    Tag.Name = "IsFake"

    local img = FakeSlot:FindFirstChildWhichIsA("ImageLabel", true)
    local txt = FakeSlot:FindFirstChildWhichIsA("TextLabel", true)

    if img then img.Image = itemData.Image end
    if txt then txt.Text = itemData.Name end

    FakeSlot.Parent = frame
end

local function MainLogic()
    if not UI_Open then return end
    
    local Player = game.Players.LocalPlayer
    local PlayerGui = Player:WaitForChild("PlayerGui")
    local TradeGui = PlayerGui:FindFirstChild("TradeGui") or PlayerGui:FindFirstChild("Trade") or PlayerGui:FindFirstChild("TradeWindow")
    
    if not TradeGui then return end
    local MyOfferFrame = TradeGui:FindFirstChild("MyOffer", true) or TradeGui:FindFirstChild("OfferFrame", true)
    if not MyOfferFrame then return end

    -- Captura
    for _, child in pairs(MyOfferFrame:GetChildren()) do
        if child:IsA("Frame") and not child:FindFirstChild("IsFake") then
            local img = child:FindFirstChildWhichIsA("ImageLabel", true)
            local txt = child:FindFirstChildWhichIsA("TextLabel", true)
            if img and txt then
                local itemID = txt.Text
                if not TrackedItems[itemID] then
                    TrackedItems[itemID] = {Image = img.Image, Name = txt.Text}
                end
            end
        end
    end

    -- Persistência
    for itemName, data in pairs(TrackedItems) do
        local found = false
        for _, child in pairs(MyOfferFrame:GetChildren()) do
            if child:IsA("Frame") and child:FindFirstChildWhichIsA("TextLabel", true) and child:FindFirstChildWhichIsA("TextLabel", true).Text == itemName then
                found = true
                break
            end
        end
        if not found then
            InjectFakeSlot(MyOfferFrame, data)
        end
    end
end

-- Botão de Abrir/Fechar UI
ToggleBtn.MouseButton1Click:Connect(function()
    UI_Open = not UI_Open
    if UI_Open then
        ToggleBtn.Text = "DESATIVAR SCRIPT"
        ToggleBtn.BackgroundColor3 = Theme.Accent
        StatusLabel.Text = "Status: ATIVADO ✅"
        StatusLabel.TextColor3 = Color3.fromRGB(0, 255, 150)
    else
        ToggleBtn.Text = "ATIVAR SCRIPT"
        ToggleBtn.BackgroundColor3 = Color3.fromRGB(100, 0, 0)
        StatusLabel.Text = "Status: DESATIVADO ❌"
        StatusLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
    end
end)

-- Loop de Execução
spawn(function()
    while true do
        pcall(MainLogic)
        task.wait(0.1)
    end
end)

-- Arrastar UI (Draggable)
local UserInputService = game:GetService("UserInputService")
local dragging, dragInput, dragStart, startPos

MainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = MainFrame.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
        dragInput = input
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

game:GetService("RunService").RenderStepped:Connect(function()
    if dragging and dragInput then
        local delta = dragInput.Position - dragStart
        MainFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
    end
end)
