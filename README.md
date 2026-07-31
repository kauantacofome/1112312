local plr = game.Players.LocalPlayer
local char = plr.Character
if not char then char = plr.CharacterAdded:Wait() end
local hrp = char:FindFirstChild("HumanoidRootPart")
local hum = char:FindFirstChild("Humanoid")
local pg = plr:PlayerGui

local function tp(x,y,z)
    if hrp then hrp.CFrame = CFrame.new(x, y+3, z) end
end

local function fireP(npc)
    local h = npc:FindFirstChild("HumanoidRootPart")
    if h then
        local p = h:FindFirstChild("DialoguePrompt")
        if p then fireproximityprompt(p) return true end
    end
    return false
end

local function click(t)
    for _, g in ipairs(pg:GetChildren()) do
        if g:IsA("ScreenGui") then
            for _, o in ipairs(g:GetDescendants()) do
                if o:IsA("TextButton") and o.Visible then
                    local txt = o.Text
                    if txt and txt ~= "" and txt ~= "Text" then
                        if string.find(string.lower(txt), string.lower(t)) then
                            o:Fire()
                            return true
                        end
                    end
                end
            end
        end
    end
    return false
end

local function getTarget()
    local q = pg:FindFirstChild("QuestUI")
    if q then
        local ti = q:FindFirstChild("Title", true)
        if ti and ti:IsA("TextLabel") then
            local txt = ti.Text
            if txt and txt ~= "" and txt ~= "Text" then
                local n = txt:gsub("DELIVER TO ",""):gsub("deliver to ","")
                if n ~= "" and n ~= "Text" and string.len(n) > 2 then
                    return n
                end
            end
        end
    end
    return nil
end

local function findNPC(n)
    local f = workspace:FindFirstChild("NPCs")
    if f then return f:FindFirstChild(n) end
    return nil
end

local function cycle()
    local b = findNPC("Fujiwara Bunta")
    if not b then task.wait(5) return end

    tp(-148.88,-12.23,667.42)
    task.wait(1.5)
    fireP(b)
    task.wait(2)
    click("job for me")
    task.wait(2)
    click("Sure")
    task.wait(3)

    local tn = getTarget()
    if not tn then task.wait(3) tn = getTarget() end
    if not tn then task.wait(5) return end

    local npc = findNPC(tn)
    if not npc then task.wait(5) return end

    local p = npc:GetPivot().Position
    tp(p.X, p.Y, p.Z)
    task.wait(1.5)

    fireP(npc)
    task.wait(2)
    click("I have something for you")
    task.wait(5)
end

while true do
    char = plr.Character
    if char then
        hrp = char:FindFirstChild("HumanoidRootPart")
        hum = char:FindFirstChild("Humanoid")
    end
    local ok = pcall(cycle)
    if not ok then task.wait(5) else task.wait(2) end
end
