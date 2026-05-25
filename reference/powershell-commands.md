# PowerShell Commands for File Operations

Quick reference for common file operations on Windows/PowerShell.

## Navigation & Listing

```powershell
# List all files and folders (no recursion)
Get-ChildItem -LiteralPath "C:\Path"

# List with file sizes
Get-ChildItem -LiteralPath "C:\Path" -File | Select-Object Name, Length, LastWriteTime

# Recursive listing
Get-ChildItem -LiteralPath "C:\Path" -Recurse

# Filter by extension
Get-ChildItem -Recurse -Filter "*.pdf"

# Hidden files
Get-ChildItem -Force  # includes hidden/system items
```

## File Analysis

```powershell
# Count by extension
Get-ChildItem -Recurse -File | Group-Object Extension | Sort-Object Count -Descending

# Largest files
Get-ChildItem -Recurse -File | Sort-Object Length -Descending | Select-Object -First 20

# Files by year
Get-ChildItem -Recurse -File | Group-Object { $_.LastWriteTime.Year }

# Total size of a folder
(Get-ChildItem -Recurse -File | Measure-Object Length -Sum).Sum

# Format size in human-readable
$bytes = (Get-ChildItem -Recurse -File | Measure-Object Length -Sum).Sum
"{0:N2} GB" -f ($bytes / 1GB)
```

## Duplicate Detection

```powershell
# Phase 1: Group by file size (fast)
$files = Get-ChildItem -Recurse -File
$sizeGroups = $files | Group-Object Length | Where-Object Count -gt 1

# Phase 2: Hash only same-sized files (slower but precise)
$dupes = $sizeGroups | ForEach-Object {
    $_.Group | Get-FileHash -Algorithm MD5
} | Group-Object Hash | Where-Object Count -gt 1

# Phase 3: Display results
$dupes | ForEach-Object {
    Write-Host "`nDuplicate Hash: $($_.Name)"
    $_.Group | ForEach-Object {
        Write-Host "  - $($_.Path) ($(Get-Item $_.Path | Select-Object -ExpandProperty Length))"
    }
}
```

## Moving & Copying

```powershell
# Move single file
Move-Item -LiteralPath "source\file.pdf" -Destination "target\"

# Move multiple files
Get-ChildItem "source\*.txt" | Move-Item -Destination "target\"

# Copy directory structure (robocopy for large ops)
Copy-Item -Recurse -LiteralPath "source" -Destination "target"

# Robocopy (better for large/cross-drive)
robocopy "C:\source" "D:\target" /E /R:2 /W:5
```

## Renaming

```powershell
# Simple rename
Rename-Item -LiteralPath "old.txt" -NewName "new.txt"

# Batch rename with counter
$i = 1
Get-ChildItem *.jpg | Rename-Item -NewName { "photo-$($script:i++).jpg" }

# Batch rename with date pattern
Get-ChildItem *.jpg | ForEach-Object {
    $newName = "{0:yyyy-MM-dd} - {1}" -f $_.LastWriteTime, $_.Name
    Rename-Item -LiteralPath $_.FullName -NewName $newName
}

# Replace text in filenames
Get-ChildItem | Where-Object { $_.Name -like "*oldtext*" } | ForEach-Object {
    $newName = $_.Name -replace "oldtext", "newtext"
    Rename-Item -LiteralPath $_.FullName -NewName $newName
}
```

## Deletion (Safe)

```powershell
# Move to Recycle Bin (NOT permanent delete)
Add-Type -AssemblyName Microsoft.VisualBasic
[Microsoft.VisualBasic.FileIO.FileSystem]::DeleteFile(
    'C:\path\to\file.txt',
    'OnlyErrorDialogs',
    'SendToRecycleBin'
)

# For directories (to Recycle Bin)
[Microsoft.VisualBasic.FileIO.FileSystem]::DeleteDirectory(
    'C:\path\to\folder',
    'OnlyErrorDialogs',
    'SendToRecycleBin'
)
```

## Creating Structure

```powershell
# Create nested directories
New-Item -ItemType Directory -Path "C:\Parent\Child\Grandchild" -Force

# Create multiple directories at once
@("Work", "Personal", "Archive") | ForEach-Object {
    New-Item -ItemType Directory -Path "C:\Target\$_" -Force
}
```

## Date/Time Filtering

```powershell
# Files modified in last N days
Get-ChildItem -Recurse -File | Where-Object {
    $_.LastWriteTime -gt (Get-Date).AddDays(-7)
}

# Files NOT accessed in N months
Get-ChildItem -Recurse -File | Where-Object {
    $_.LastAccessTime -lt (Get-Date).AddMonths(-6)
}

# Files from specific year
Get-ChildItem -Recurse -File | Where-Object {
    $_.LastWriteTime.Year -eq 2024
}
```

## Preview (WhatIf)

```powershell
# Preview move without executing
Move-Item -LiteralPath "source\file.txt" -Destination "target\" -WhatIf

# Preview batch rename
Get-ChildItem *.jpg | Rename-Item -NewName { "new-$($_.Name)" } -WhatIf
```

## Path Utilities

```powershell
# Safe path joining
Join-Path "C:\Parent" "Child\file.txt"

# Get user folders
$env:USERPROFILE        # C:\Users\username
$env:USERNAME            # username
[Environment]::GetFolderPath('Desktop')
[Environment]::GetFolderPath('MyDocuments')
[Environment]::GetFolderPath('Downloads')  # Note: May need GetFolderPath('UserProfile') + '\Downloads'
```

## Logging

```powershell
# Write action log
$logEntry = "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Moved: '$source' → '$dest'"
$logEntry | Out-File -FilePath "C:\Target\organize-log.txt" -Append -Encoding UTF8
```
