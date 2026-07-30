-- โหลด Rayfield UI Library
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
local Window = Rayfield:CreateWindow({Name = "Pet Hospital Hub", LoadingTitle = "Loading Script...", ConfigurationSaving = {Enabled = false}})

local Players, RunService, Workspace = game:GetService("Players"), game:GetService("RunService"), game:GetService("Workspace")
local LP = Players.LocalPlayer
local espMonsterOn = false

-- ตารางบันทึกตำแหน่งก่อนหน้าเพื่อตรวจจับการสั่นผิดปกติ (Shaking Check)
local lastPositions = {}

-- ฟังก์ชันตรวจสอบพฤติกรรมผิดปกติ / ปีศาจ
local function isMonster(model)
   local name = model.Name:lower()
   
   -- 1. เช็คจากชื่อหรือ Attribute
   if name:find("monster") or name:find("anomaly") or name:find("fake") or name:find("demon") then return true end
   if model:FindFirstChild("IsMonster") or model:GetAttribute("IsMonster") or model:GetAttribute("Anomaly") then return true end
   
   -- 2. ตรวจจับหัวเคาะกระจก / ชนเคาน์เตอร์
   local head = model:FindFirstChild("Head")
   if head then
      for _, part in pairs(Workspace:GetDescendants()) do
         if part:IsA("BasePart") and (part.Name:lower():find("glass") or part.Name:lower():find("counter") or part.Name:lower():find("window")) then
            if (head.Position - part.Position).Magnitude < 2.5 then
               return true
            end
         end
      end
   end
   
   -- 3. ตรวจจับอาการสั่นผิดปกติที่ แขน ขา หัว (Shaking / Jittering)
   local hrp = model:FindFirstChild("HumanoidRootPart") or head
   if hrp then
      local lastPos = lastPositions[model]
      local currentPos = hrp.Position
      
      if lastPos then
         local vel = (currentPos - lastPos).Magnitude
         -- ถ้าเคลื่อนที่หยุกหยกสั่นรวดเร็วเกินมนุษย์ปกติ
         if vel > 0.8 and vel < 3 and (model:FindFirstChildOfClass("Humanoid") and model.Humanoid.MoveDirection.Magnitude == 0) then
            lastPositions[model] = currentPos
            return true
         end
      end
      lastPositions[model] = currentPos
   end
   
   return false
end

-- ลูปอัปเดต ESP แยกสีคน/ปีศาจ
RunService.RenderStepped:Connect(function()
   if not espMonsterOn then return end
   
   for _, v in pairs(Workspace:GetDescendants()) do
      if v:IsA("Model") and v:FindFirstChildOfClass("Humanoid") and v ~= LP.Character then
         local highlight = v:FindFirstChild("Hospital_ESP") or Instance.new("Highlight", v)
         highlight.Name = "Hospital_ESP"
         
         if isMonster(v) then
            highlight.FillColor = Color3.new(1, 0, 0) -- 🔴 สีแดงเมื่อเป็นปีศาจ/สั่น/เคาะกระจก
            highlight.OutlineColor = Color3.new(1, 1, 1)
         else
            highlight.FillColor = Color3.new(0, 1, 0) -- 🟢 สีเขียวคนปกติ
            highlight.OutlineColor = Color3.new(0, 0, 0)
         end
      end
   end
end)

-- UI TABS
local Tab = Window:CreateTab("หน้าหลัก", 4483362458)

-- --- SECTION 1: ระบบตรวจจับปีศาจ ---
Tab:CreateSection("1. ตรวจจับปีศาจ (Anomaly Detector)")

Tab:CreateToggle({
   Name = "👁️ เปิด ESP (แดง = ปีศาจ/สั่น/เคาะกระจก | เขียว = คนปกติ)",
   CurrentValue = false,
   Callback = function(v)
      espMonsterOn = v
      if not v then
         for _, v in pairs(Workspace:GetDescendants()) do
            if v:FindFirstChild("Hospital_ESP") then
               v.Hospital_ESP:Destroy()
            end
         end
      end
   end
})

-- --- SECTION 2: ระบบวาร์ปอัตโนมัติ ---
Tab:CreateSection("2. ระบบวาร์ป (Teleports)")

Tab:CreateButton({
   Name = "🏢 วาร์ปไปเคาน์เตอร์อัตโนมัติ",
   Callback = function()
      local char = LP.Character
      if not (char and char:FindFirstChild("HumanoidRootPart")) then return end
      
      local found = false
      for _, obj in pairs(Workspace:GetDescendants()) do
         local name = obj.Name:lower()
         if (name:find("counter") or name:find("reception") or name:find("desk") or name:find("register")) and (obj:IsA("BasePart") or obj:IsA("Model")) then
            local targetCFrame = obj:IsA("BasePart") and obj.CFrame or obj:GetPivot()
            char.HumanoidRootPart.CFrame = targetCFrame + Vector3.new(0, 3, 2)
            found = true
            break
         end
      end
      
      if not found then
         Rayfield:Notify({
            Title = "แจ้งเตือน",
            Content = "ไม่พบจุดเคาน์เตอร์ในบริเวณใกล้เคียง",
            Duration = 3,
            Image = 4483362458
         })
      end
   end
})

Tab:CreateButton({
   Name = "💊 วาร์ปไปตู้ยา/ชั้นวางยา",
   Callback = function()
      local char = LP.Character
      if not (char and char:FindFirstChild("HumanoidRootPart")) then return end
      
      local found = false
      for _, obj in pairs(Workspace:GetDescendants()) do
         local name = obj.Name:lower()
         if (name:find("med") or name:find("pharmacy") or name:find("medicine") or name:find("pill") or name:find("cure")) and (obj:IsA("BasePart") or obj:IsA("Model")) then
            local targetCFrame = obj:IsA("BasePart") and obj.CFrame or obj:GetPivot()
            char.HumanoidRootPart.CFrame = targetCFrame + Vector3.new(0, 3, 0)
            found = true
            break
         end
      end
      
      if not found then
         Rayfield:Notify({
            Title = "แจ้งเตือน",
            Content = "ไม่พบจุดเก็บยาในบริเวณใกล้เคียง",
            Duration = 3,
            Image = 4483362458
         })
      end
   end
})
