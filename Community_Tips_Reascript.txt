-- Set the script title and unique ID
local scriptTitle = "Community Ideas"
local scriptID = 20000 -- Replace with a unique ID of your choice

-- Define the function to clear the Reaper Console
function clearReaperConsole()
    local info = debug.getinfo(1, "S")
    reaper.ShowConsoleMsg("")
end

-- Define the function to populate text from the spreadsheet
function populateTextFromSpreadsheet()
    -- Enter the path to your spreadsheet file
    local spreadsheetPath = “PASTE YOUR FILE PATH HERE - KEEP QUOTATIONS”

    -- Load the spreadsheet file
    local file = io.open(spreadsheetPath, "r")
    if not file then
        reaper.MB("Failed to open the spreadsheet file.", "Error", 0)
        return
    end

    -- Read all lines from the spreadsheet file
    local lines = {}
    for line in file:lines() do
        table.insert(lines, line)
    end
    file:close()

    -- Check if the spreadsheet contains data
    if #lines < 1 then
        reaper.MB("The spreadsheet is empty.", "Error", 0)
        return
    end

    -- Select a random row from the spreadsheet
    local randomRow = lines[math.random(#lines)]

    -- Split the row into individual cells
    local cells = {}
    for cell in randomRow:gmatch("[^,]+") do
        table.insert(cells, cell)
    end

    -- Check if the row has at least 3 cells
    if #cells < 3 then
        reaper.MB("The selected row does not have enough cells.", "Error", 0)
        return
    end

    -- Format the text with each cell on a separate line
    local text = cells[1] .. "\n\n" .. cells[2] .. "\n" .. cells[3]

    -- Clear the Reaper Console before displaying the new text
    clearReaperConsole()

    -- Show the text in the Reaper Console
    reaper.ShowConsoleMsg(text .. "\n")
end

-- Register the script as a command in Reaper
reaper.SetExtState("MyScript", tostring(scriptID), "command", false)
reaper.SetExtState("MyScript", tostring(scriptID) .. "_desc", scriptTitle, false)

-- Define the function to handle the script execution
function runScript(commandID)
    if commandID == scriptID then
        populateTextFromSpreadsheet()
    end
end

-- Run the script execution function when the command is called
local commandID = scriptID
runScript(commandID)
