-- [[ Marvellous Smackdown - Kira Gelişmiş Strateji Scripti v10 ]] --
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

_G.PurpleESP = false
_G.SpeedHack = false
_G.BigHitbox = false
_G.BringEnemies = false

-- // MENÜ TASARIMI
local ScreenGui = Instance.new("ScreenGui")
pcall(function() ScreenGui.Parent = game.CoreGui end)
if not ScreenGui.Parent then ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui") end

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 200, 0, 240)
MainFrame.Position = UDim2.new(0.65, 0, 0.25, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(25, 15, 35) -- Derin mor tema
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 35)
Title.BackgroundColor3 = Color3.fromRGB(50, 20, 70)
Title.Text = "Kira Strateji Modu v10"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Font = Enum.Font.SourceSansBold
Title.Parent = MainFrame

local function createBtn(text, pos)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0.9, 0, 0, 35)
    btn.Position = pos
    btn.BackgroundColor3 = Color3.fromRGB(130, 20, 150)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Text = text
    btn.Parent = MainFrame
    return btn
end

local BtnESP = createBtn("Mor ESP: KAPALI", UDim2.new(0.05, 0, 0.2, 0))
local BtnHitbox = createBtn("Hitbox Devleştirici: KAPALI", UDim2.new(0.05, 0, 0.4, 0))
local BtnBring = createBtn("Herkesi Önüne Çek: KAPALI", UDim2.new(0.05, 0, 0.6, 0))
local BtnSpeed = createBtn("Hız Hilesi: KAPALI", UDim2.new(0.05, 0, 0.8, 0))

-- // 1. MOR ESP (WALLHACK) SİSTEMİ
task.spawn(function()
    while true do
        task.wait(1)
        if _G.PurpleESP then
            pcall(function()
                for _, plr in pairs(Players:GetPlayers()) do
                    if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                        if not plr.Character:FindFirstChild("PurpleHighlight") then
                            local hl = Instance.new("Highlight")
                            hl.Name = "PurpleHighlight"
                            hl.FillColor = Color3.fromRGB(150, 0, 255) -- Parlak Mor
                            hl.FillTransparency = 0.4
                            hl.OutlineColor = Color3.fromRGB(255, 255, 255)
                            hl.OutlineTransparency = 0
                            hl.Adornee = plr.Character
                            hl.Parent = plr.Character
                        end
                    end
                end
            end)
        else
            pcall(function()
                for _, plr in pairs(Players:GetPlayers()) do
                    if plr.Character and plr.Character:FindFirstChild("PurpleHighlight") then
                        plr.Character.PurpleHighlight:Destroy()
                    end
                end
            end)
        end
    end
end)

-- // 2. HITBOX DEVLEŞTİRİCİ (Iskalamayı İmkansız Kıl)
task.spawn(function()
    while true do
        task.wait(0.5)
        if _G.BigHitbox then
            pcall(function()
                for _, plr in pairs(Players:GetPlayers()) do
                    if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                        local hrp = plr.Character.HumanoidRootPart
                        hrp.Size = Vector3.new(20, 20, 20) -- Düşmanların tıklama alanını devasa yapar
                        hrp.Transparency = 0.7
                        hrp.Color = Color3.fromRGB(150, 0, 255)
                        hrp.CanCollide = false
                    end
                end
            end)
        end
    end
end)

-- // 3. HERKESİ ÖNÜNE ÇEKLME (Bring Players / Teleport Exploit)
task.spawn(function()
    while true do
        task.wait(0.3)
        if _G.BringEnemies and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            pcall(function()
                local myPos = LocalPlayer.Character.HumanoidRootPart.CFrame
                for _, plr in pairs(Players:GetPlayers()) do
                    if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                        -- Tüm düşmanları tam önünüze (2 birim ileriye) ışınlar, böylece düz vuruşla veya defterle tek seferde hepsini biçebilirsiniz.
                        plr.Character.HumanoidRootPart.CFrame = myPos * CFrame.new(0, 0, -3)
                    end
                end
            end)
        end
    end
end)

-- // 4. GARANTİLİ SÜPER SPRINT
task.spawn(function()
    while true do
        task.wait(0.1)
        if _G.SpeedHack and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
            LocalPlayer.Character.Humanoid.WalkSpeed = 45
        end
    end
end)

-- // BUTON TETİKLEYİCİLERİ
local function updateBtnState(btn, condition, textOn, textOff)
    btn.Text = condition and textOn or textOff
    btn.BackgroundColor3 = condition and Color3.fromRGB(0, 150, 0) or Color3.fromRGB(130, 20, 150)
end

BtnESP.MouseButton1Click:Connect(function()
    _G.PurpleESP = not _G.PurpleESP
    updateBtnState(BtnESP, _G.PurpleESP, "Mor ESP: AÇIK", "Mor ESP: KAPALI")
end)

BtnHitbox.MouseButton1Click:Connect(function()
    _G.BigHitbox = not _G.BigHitbox
    updateBtnState(BtnHitbox, _G.BigHitbox, "Hitbox: DEV MOD", "Hitbox Devleştirici: KAPALI")
    if not _G.BigHitbox then
        for _, plr in pairs(Players:GetPlayers()) do
            pcall(function() plr.Character.HumanoidRootPart.Size = Vector3.new(2, 2, 1) plr.Character.HumanoidRootPart.Transparency = 0 end)
        end
    end
end)

BtnBring.MouseButton1Click:Connect(function()
    _G.BringEnemies = not _G.BringEnemies
    updateBtnState(BtnBring, _G.BringEnemies, "Bring: AKTİF", "Herkesi Önüne Çek: KAPALI")
end)

BtnSpeed.MouseButton1Click:Connect(function()
    _G.SpeedHack = not _G.SpeedHack
    updateBtnState(BtnSpeed, _G.SpeedHack, "Süper Sprint: AÇIK", "Hız Hilesi: KAPALI")
    if not _G.SpeedHack and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 16
    end
end)
