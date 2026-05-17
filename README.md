-- Mobile Özel Tasarım: Afganistan Sıfır Tepme Menüsü
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local CoreGui = game:GetService("CoreGui")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Ayarlar
getgenv().NoRecoil = false -- Geri Tepme Engelleyici
getgenv().Aimbot = false -- Otomatik Hedef Kilitleme
getgenv().EspWallhack = false -- Dağ Arkası Röntgen
getgenv().TacticalSpeed = false -- Güvenli Hız Takviyesi

-- ==========================================
-- MOBİL ARAYÜZ (GUI) TASARIMI
-- ==========================================
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "Afghanistan_MobileUI"
ScreenGui.ResetOnSpawn = false
local parent = CoreGui:FindFirstChild("RobloxGui") or LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.Parent = parent

-- Ana Menü
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 230, 0, 260)
MainFrame.Position = UDim2.new(0.1, 0, 0.25, 0)
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 30, 25) -- Çöl ve kaya temalı koyu kahve/bej
MainFrame.BorderSizePixel = 0
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Visible = true
MainFrame.Parent = ScreenGui

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 12)
MainCorner.Parent = MainFrame

-- Başlık
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 40)
Title.BackgroundColor3 = Color3.fromRGB(50, 40, 35)
Title.Text = "  AFGHANISTAN AI MENU"
Title.TextColor3 = Color3.fromRGB(240, 230, 210)
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 14
Title.Parent = MainFrame

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 12)
TitleCorner.Parent = Title

-- Liste Düzeni
local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Padding = UDim.new(0, 8)
UIListLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Parent = MainFrame

local Pad = Instance.new("Frame")
Pad.Size = UDim2.new(1, 0, 0, 45)
Pad.BackgroundTransparency = 1
Pad.LayoutOrder = 0
Pad.Parent = MainFrame

-- BUTON FONKSİYONU
local function createButton(text, layoutOrder, callback)
    local Btn = Instance.new("TextButton")
    Btn.Size = UDim2.new(0, 200, 0, 38)
    Btn.BackgroundColor3 = Color3.fromRGB(55, 50, 45)
    Btn.Text = text .. " [KAPALI]"
    Btn.TextColor3 = Color3.fromRGB(200, 190, 180)
    Btn.Font = Enum.Font.SourceSansBold
    Btn.TextSize = 13
    Btn.LayoutOrder = layoutOrder
    Btn.Parent = MainFrame
    
    local BtnCorner = Instance.new("UICorner")
    BtnCorner.CornerRadius = UDim.new(0, 6)
    BtnCorner.Parent = Btn
    
    local state = false
    Btn.MouseButton1Click:Connect(function()
        state = not state
        if state then
            Btn.BackgroundColor3 = Color3.fromRGB(200, 150, 80) -- Çöl sarısı / Altın rengi aktif butonu
            Btn.TextColor3 = Color3.fromRGB(0, 0, 0)
            Btn.Text = text .. " [AKTİF]"
        else
            Btn.BackgroundColor3 = Color3.fromRGB(55, 50, 45)
            Btn.TextColor3 = Color3.fromRGB(200, 190, 180)
            Btn.Text = text .. " [KAPALI]"
        end
        callback(state)
    end)
end

-- Taktik Butonları Menüye Ekle
createButton("Sıfır Geri Tepme (No Recoil)", 1, function(v) getgenv().NoRecoil = v end)
createButton("Yumuşak Kilitlenme (Aimbot)", 2, function(v) getgenv().Aimbot = v end)
createButton("Arazi Röntgeni (ESP)", 3, function(v) getgenv().EspWallhack = v end)
createButton("Taktik Koşu Hızı", 4, function(v) getgenv().TacticalSpeed = v end)

-- MENÜ AÇ/KAPAT BUTONU
local ToggleMenuBtn = Instance.new("TextButton")
ToggleMenuBtn.Size = UDim2.new(0, 65, 0, 65)
ToggleMenuBtn.Position = UDim2.new(0.02, 0, 0.25, 0)
ToggleMenuBtn.BackgroundColor3 = Color3.fromRGB(200, 150, 80)
ToggleMenuBtn.Text = "OPERASYON"
ToggleMenuBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
ToggleMenuBtn.Font = Enum.Font.SourceSansBold
ToggleMenuBtn.TextSize = 10
ToggleMenuBtn.Parent = ScreenGui

local MenuBtnCorner = Instance.new("UICorner")
MenuBtnCorner.CornerRadius = UDim.new(0, 32)
MenuBtnCorner.Parent = ToggleMenuBtn

ToggleMenuBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

-- ==========================================
-- TAKTİK SİSTEMLER VE MOBİL INTEGRASYON
-- ==========================================

-- En Yakın Düşmanı Seçen Algoritma
local function getClosestPlayer()
    local closestPlayer = nil
    local shortestDistance = math.huge
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
            if player.Team ~= LocalPlayer.Team or LocalPlayer.Team == nil then
                local pos, onScreen = Camera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position)
                if onScreen then
                    local mousePos = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
                    local distance = (Vector2.new(pos.X, pos.Y) - mousePos).Magnitude
                    if distance < shortestDistance and distance < 250 then
                        shortestDistance = distance
                        closestPlayer = player
                    end
                end
            end
        end
    end
    return closestPlayer
end

-- Arka Plan Döngüsü
RunService.RenderStepped:Connect(function()
    -- 1. Sıfır Geri Tepme (No Recoil Modülü)
    if getgenv().NoRecoil and LocalPlayer.Character then
        local tool = LocalPlayer.Character:FindFirstChildOfClass("Tool")
        if tool then
            -- Oyundaki genel silah script değişkenlerini (Recoil, CamShake, Kickback) yakalar ve sıfırlar
            for _, v in ipairs(tool:GetDescendants()) do
                if v:IsA("NumberValue") or v:IsA("IntValue") then
                    if string.find(string.lower(v.Name), "recoil") or string.find(string.lower(v.Name), "kick") or string.find(string.lower(v.Name), "shake") or string.find(string.lower(v.Name), "spread") then
                        v.Value = 0
                    end
                end
            end
        end
    end

    -- 2. Yumuşak Kilitlenme (Aimbot)
    if getgenv().Aimbot then
        local target = getClosestPlayer()
        if target and target.Character and target.Character:FindFirstChild("Head") then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character.Head.Position)
        end
    end

    -- 3. Arazi Röntgeni (ESP)
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local hrp = player.Character.HumanoidRootPart
            local highlight = hrp:FindFirstChild("AfghanistanESP")
            
            if getgenv().EspWallhack and (player.Team ~= LocalPlayer.Team or LocalPlayer.Team == nil) then
                if not highlight then
                    local box = Instance.new("BoxHandleAdornment")
                    box.Name = "AfghanistanESP"
                    box.Size = Vector3.new(4, 6, 4)
                    box.Color3 = Color3.fromRGB(255, 65, 65) -- Düşman askerleri belirgin kırmızı görünür
                    box.AlwaysOnTop = true
                    box.ZIndex = 10
                    box.Transparency = 0.6
                    box.Adornee = hrp
                    box.Parent = hrp
                end
            else
                if highlight then highlight:Destroy() end
            end
        end
    end

    -- 4. Taktik Koşu Hızı (Ban Riski Yaratmayan Hafif Çarpan)
    if getgenv().TacticalSpeed and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 22 -- Çöl arazisinde rahat hareket etmek için ideal hız
    elseif not getgenv().TacticalSpeed and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 16
    end
end)

print("[AFGHANISTAN AI]: Tepme engelleyici ve taktik menü başarıyla yüklendi!")
