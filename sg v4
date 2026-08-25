local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer

-- --- BIẾN ---
local currentOffset = CFrame.new(0, 0, 0)
local activeGhost, activeWeld = nil, nil
local cachedRoot, cachedTorso = nil, nil

-- --- HÀM TÍNH CHIỀU CAO (ĐỂ TỰ CÂN TỶ LỆ) ---
local function calculateHeight()
    local char = player.Character
    if not char then return 5 end
    local root = char:FindFirstChild("HumanoidRootPart")
    local foot = char:FindFirstChild("LeftFoot") or char:FindFirstChild("LeftLeg")
    
    if root and foot then
        return math.abs(root.Position.Y - foot.Position.Y)
    end
    return 5
end

-- --- LOGIC TẠO GHOST ---
local function deployGhostGlitch()
    local char = player.Character
    if not char then return end
    
    cachedRoot = char:FindFirstChild("HumanoidRootPart")
    cachedTorso = char:FindFirstChild("Torso") or char:FindFirstChild("UpperTorso")
    
    if not cachedTorso or not cachedRoot then return end

    -- TÍNH TOÁN DỰA TRÊN VỊ TRÍ GỐC (RAW)
    local rawCF = cachedRoot.CFrame:ToObjectSpace(cachedTorso.CFrame)
    
    -- TÍNH HỆ SỐ NHÂN
    local height = calculateHeight()
    local multiplier = 30 -- Mặc định cho nhân vật thường
    
    -- Nếu chiều cao khác biệt đáng kể với chuẩn (5), mới tính lại tỷ lệ
    if height < 4 or height > 6 then
        multiplier = 24 * (height / 3)
    end
    
    -- Cập nhật Offset dựa trên mốc gốc (không dùng lại offset cũ)
    local newPos = rawCF.Position * multiplier
    currentOffset = CFrame.new(newPos) * rawCF.Rotation

    -- Xóa ghost cũ
    local oldGhost = char:FindFirstChild("Skibidi_Ghost_Active")
    if oldGhost then oldGhost:Destroy() end

    -- Tạo Ghost mới
    local newGhost = Instance.new("Part")
    newGhost.Name = "Skibidi_Ghost_Active"
    newGhost.Size = Vector3.new(1, 1, 1)
    newGhost.CFrame = cachedTorso.CFrame 
    newGhost.Transparency = 1 
    newGhost.CanCollide = false
    newGhost.Parent = char

    -- Tạo Weld
    activeWeld = Instance.new("Weld")
    activeWeld.Part0 = cachedTorso
    activeWeld.Part1 = newGhost
    activeWeld.C0 = currentOffset
    activeWeld.Parent = newGhost

    activeGhost = newGhost
end

-- --- LẮNG NGHE SỰ KIỆN ---
local function setupListeners(char)
    if not char then return end
    
    local humanoid = char:FindFirstChild("Humanoid")
    if humanoid then
        humanoid.Died:Connect(function()
            activeGhost = nil
            activeWeld = nil
            cachedRoot = nil
            cachedTorso = nil
        end)
    end
    
    task.defer(deployGhostGlitch)
    
    char.ChildAdded:Connect(function(child) if child:IsA("Tool") then deployGhostGlitch() end end)
    char.ChildRemoved:Connect(function(child) if child:IsA("Tool") then deployGhostGlitch() end end)
end

if player.Character then setupListeners(player.Character) end
player.CharacterAdded:Connect(setupListeners)

-- --- VÒNG LẶP DUY TRÌ ---
RunService.Heartbeat:Connect(function()
    if activeWeld then
        -- Update lại weld theo Offset đã tính chuẩn
        activeWeld.C0 = currentOffset
    else
        -- Nếu weld mất thì tạo lại
        if player.Character and (not activeGhost or activeGhost.Parent ~= player.Character) then
            deployGhostGlitch()
        end
    end
end)
