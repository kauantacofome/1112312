local plr = game.Players.LocalPlayer
local char = plr.Character or plr.CharacterAdded:Wait()
local hrp = char:FindFirstChild("HumanoidRootPart")
local hum = char:FindFirstChild("Humanoid")
local pg = plr:PlayerGui

local NPC_POS = {
    ["Fujiwara Bunta"] = Vector3.new(-148.88, -12.23, 667.42),
    ["Sayama Kaoru"] = Vector3.new(-188.37, -9.77, 602.55),
    ["Shirogane Kazuya"] = Vector3.new(-114.50, -7.86, 507.25),
    ["Motsu Ren"] = Vector3.new(-55.19, -2.09, 484.99),
    ["Kobayashi Ren"] = Vector3.new(-118.23, -13.05, 641.25),
    ["Construction Baka"] = Vector3.new(-26.70, 1.96, 452.39),
    ["Hayakawa Aki"] = Vector3.new(-373.72, -5.52, 374.16),
    ["Farina Lucia"] = Vector3.new(-745.42, -23.87, 481.45),
    ["Takahashi Ren"] = Vector3.new(-761.00, -24.80, 405.66),
    ["Nakamura Aika"] = Vector3.new(-793.22, -24.76, 459.17),
    ["Yasha Akuma Saidai"] = Vector3.new(-213.95, 7.32, 264.78),
    ["Wasuke Komaki Tori"] = Vector3.new(-693.47, -23.85, 325.96),
    ["Principal Ahkiyama"] = Vector3.new(-253.58, 16.69, 15.57),
    ["Old Man Aizawa"] = Vector3.new(-440.63, -5.42, 350.53),
    ["Old Man Hatori"] = Vector3.new(-812.23, -24.40, 477.62),
    ["Ukabu Azusa"] = Vector3.new(-479.08, -13.33, 638.52),
    ["Dela Cruz Yui"] = Vector3.new(-756.70, -25.38, 423.78),
    ["Akizuki Kaede"] = Vector3.new(-37.93, 0.34, -236.34),
    ["Suka Teio"] = Vector3.new(-815.73, -24.68, 494.64),
    ["Kamasaki Yuri"] = Vector3.new(-844.90, -25.32, 406.55),
    ["Sayama Himari"] = Vector3.new(-528.22, -12.96, 619.19),
    ["Kazehaya Wimu"] = Vector3.new(-22.59, 2.39, 86.82),
    ["Silvestri Kat"] = Vector3.new(-487.83, -5.60, 346.67)
}

local function teleportTo(pos)
    if hrp then
        hrp.CFrame = CFrame.new(pos + Vector3.new(0, 3, 0))
    end
end

local function firePrompt(npc)
    local hrpNPC = npc:FindFirstChild("HumanoidRootPart")
    if hrpNPC then
        local prompt = hrpNPC:FindFirstChild("DialoguePrompt")
        if prompt then
            fireproximityprompt(prompt)
            return true
        end
    end
    return false
end

local function clickText(text)
    for _, gui in ipairs(pg:GetChildren()) do
        if gui:IsA("ScreenGui") then
            for _, obj in ipairs(gui:GetDescendants()) do
                if obj:IsA("TextButton") then
                    local t = obj.Text
                    if t and t ~= "" and t ~= "Text" then
                        local tL = string.lower(t)
                        local s = string.lower(text)
                        if string.find(tL, s) then
                            if obj.Visible then
                                obj:Fire()
                                return true
                            end
                        end
                    end
                end
            end
        end
    end
    return false
end

local function getQuestTarget()
    local questUI = pg:FindFirstChild("QuestUI")
    if questUI then
        local title = questUI:FindFirstChild("Title", true)
        if title and title:IsA("TextLabel") then
            local txt = title.Text
            if txt and txt ~= "" and txt ~= "Text" then
                local name = txt:gsub("DELIVER TO ", ""):gsub("deliver to ", "")
                if name ~= "" and name ~= "Text" and string.len(name) > 2 then
                    return name
                end
            end
        end
    end
    return nil
end

local function findNPC(name)
    local npcs = workspace:FindFirstChild("NPCs")
    if npcs then
        return npcs:FindFirstChild(name)
    end
    return nil
end

local function runCycle()
    local bunta = findNPC("Fujiwara Bunta")
    if not bunta then
        task.wait(5)
        return
    end

    teleportTo(NPC_POS["Fujiwara Bunta"])
    task.wait(1.5)
    firePrompt(bunta)
    task.wait(2)

    -- Click "Do you have a job for me?"
    clickText("job for me")
    task.wait(2)

    -- Click "Sure, I'll take it"
    clickText("Sure")
    task.wait(3)

    -- Get quest target
    local targetName = getQuestTarget()
    if not targetName then
        task.wait(3)
        targetName = getQuestTarget()
    end

    if not targetName then
        task.wait(5)
        return
    end

    -- Teleport to delivery NPC
    local pos = NPC_POS[targetName]
    if not pos then
        local npc = findNPC(targetName)
        if npc then
            pos = npc:GetPivot().Position
        else
            task.wait(5)
            return
        end
    end

    teleportTo(pos)
    task.wait(1.5)

    local targetNPC = findNPC(targetName)
    if targetNPC then
        firePrompt(targetNPC)
        task.wait(2)
        clickText("I have something for you")
    end

    task.wait(5)
end

while true do
    char = plr.Character
    if char then
        hrp = char:FindFirstChild("HumanoidRootPart")
        hum = char:FindFirstChild("Humanoid")
    end
    local ok, err = pcall(runCycle)
    if not ok then
        task.wait(5)
    else
        task.wait(2)
    end
end
