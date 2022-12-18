_G.RBX_Host = "localhost"
_G.RBX_Port = "7963"
_G.Natzu = true

request = http_request or request or HttpPost or syn.request

Sword_List = {}
Accessory_List = {}
Awakened_List = {}


local id = game.PlaceId
if id == 2753915549 then world1 = true; elseif id == 4442272183 then world2 = true; elseif id == 7449423635 then world3 = true; 
    end

local foo = function(n)
   if n >= 10^6 then
       return string.format("%.2fm", n / 10^6)
   elseif n >= 10^3 then
       return string.format("%.2fk", n / 10^3)
   else
       return tostring(n)
   end
end

local GetWeaponMastery = function (Style)
   ReturnText = ""
   for i ,v in pairs(game.Players.LocalPlayer.Backpack:GetChildren()) do
      if v:IsA("Tool") then
         if v.ToolTip == Style then
            ReturnText = v:FindFirstChild("Level").Value
         end
      end
   end
   for i ,v in pairs(game.Players.LocalPlayer.Character:GetChildren()) do
      if v:IsA("Tool") then
         if v.ToolTip == Style then
            ReturnText = v:FindFirstChild("Level").Value
         end
      end
   end
   return ReturnText
end

local GetWeaponMastery2 = function(Name)
   for i , v in pairs(game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer('getInventory')) do
      if type(v) == "table" then
         if v.Name == Name then
            return tostring(v.Mastery)
         end
      end
   end
end

local Send = function()

   print("Starting")

   for i in pairs(Sword_List) do
      Sword_List[i] = nil
   end

   for i in pairs(Accessory_List) do
      Accessory_List[i] = nil
   end

   for i in pairs(Awakened_List) do
      Awakened_List[i] = nil
   end

   for i , v in pairs(game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer('getInventory')) do
      if type(v) == "table" then
         if v.Type == "Sword" then
            table.insert(Sword_List, v.Name)
         end
      end
   end
    
   for i , v in pairs(game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer('getInventory')) do
      if type(v) == "table" then
         if v.Type == "Wear" then
            table.insert(Accessory_List, v.Name)
         end
      end
   end
if world2 or world3 then
   for i , v in pairs(game:GetService("ReplicatedStorage").Remotes.CommF_:InvokeServer("getAwakenedAbilities")) do
      if type(v) == "table" then
         if v.Awakened == true then
            table.insert(Awakened_List, v.Key)
         end
      end
   end
  end





   if world1 then
      Alias = "World 1"
   elseif world2 then
      Alias = "World 2"
   elseif world3 then
      Alias = "World 3"
   end
   
   request({
      Url = "http://".. _G.RBX_Host ..":".. _G.RBX_Port .."/SetAlias?Account=".. game.Players.LocalPlayer.Name,
      Method = "POST",
      Body = Alias
   })

   local Level = game:GetService("Players").LocalPlayer.Data.Level.Value
   local Beli = foo(game:GetService("Players").LocalPlayer.Data.Beli.Value)
   local Fragment = foo(game:GetService("Players").LocalPlayer.Data.Fragments.Value)

   local Fruit = game:GetService("Players").LocalPlayer.Data.DevilFruit.Value
   local MasteryFruit = GetWeaponMastery("Blox Fruit")

   AllAwakened = ""
   for i , v in pairs(Awakened_List) do
      AllAwakened = AllAwakened .. v .. " "
   end

   local AllSword = ""
   for i , v in pairs(Sword_List) do
      AllSword = AllSword .. v .. " (M:" .. GetWeaponMastery2(v) .. ") "
   end

   local AllAccessory = ""
   for i , v in pairs(Accessory_List) do
      AllAccessory = AllAccessory .. v .. " (M:" .. GetWeaponMastery2(v) .. ") "
   end

   Description = ([[
%s Lv. %s %s F
%s ( %s | M:%s )
-- [ Sword ] -- 
[ %s ]
-- [ Accessory ] --
[ %s ]
]]):format(Level, Beli, Fragment, Fruit, AllAwakened, MasteryFruit, AllSword, AllAccessory)

   request({
      Url = "http://".. _G.RBX_Host ..":".. _G.RBX_Port .."/SetDescription?Account=".. game.Players.LocalPlayer.Name,
      Method = "POST",
      Body = Description
   })

   print("Success")

end
spawn(function()
    while wait(5) do
        if _G.Natzu  then
repeat wait(20) 
Send()
until  world1 == false
        end
    end
end)
