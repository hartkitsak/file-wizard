# PowerShell Quick Reference

## Navigation & Analysis
```powershell
Get-ChildItem -LiteralPath "path" -Force                    # list all (incl hidden)
Get-ChildItem -Recurse -File | Group-Object Extension | Sort Count -Descending  # by type
Get-ChildItem -Recurse -File | Sort Length -Descending | Select -First 20       # largest
(Get-ChildItem -Recurse -File | Measure Length -Sum).Sum    # total size
Get-ChildItem -Recurse -File | Group-Object { $_.LastWriteTime.Year }  # by year
```

## Duplicate Detection
```powershell
$files = Get-ChildItem -Recurse -File
$bySize = $files | Group-Object Length | Where Count -gt 1
$dupes = $bySize | ForEach-Object { $_.Group | Get-FileHash -Algorithm MD5 } |
    Group-Object Hash | Where-Object Count -gt 1
```

## Safe Deletion (Recycle Bin)
```powershell
Add-Type -AssemblyName Microsoft.VisualBasic
[Microsoft.VisualBasic.FileIO.FileSystem]::DeleteFile('path','OnlyErrorDialogs','SendToRecycleBin')
# For directories:
[Microsoft.VisualBasic.FileIO.FileSystem]::DeleteDirectory('path','OnlyErrorDialogs','SendToRecycleBin')
```

## Batch Rename
```powershell
$i = 1; Get-ChildItem *.jpg | Rename-Item -NewName { "photo-$($script:i++).jpg" }
Get-ChildItem | Where Name -like "*old*" | Rename-Item -NewName { $_.Name -replace "old","new" }
```

## Date Filtering
```powershell
# Last 7 days
Get-ChildItem -Recurse -File | Where LastWriteTime -gt (Get-Date).AddDays(-7)
# Not accessed in 6 months
Get-ChildItem -Recurse -File | Where LastAccessTime -lt (Get-Date).AddMonths(-6)
```

## Preview (WhatIf)
```powershell
Move-Item -LiteralPath "src" -Destination "dst" -WhatIf
Get-ChildItem *.jpg | Rename-Item -NewName { "new-$($_.Name)" } -WhatIf
```

## Utilities
```powershell
Join-Path "C:\Parent" "Child"          # safe path joining
$env:USERPROFILE                       # C:\Users\username
[Environment]::GetFolderPath('Desktop')
# Logging
$log = "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Moved: '$src' → '$dst'"
$log | Out-File "log.txt" -Append -Encoding UTF8
```
