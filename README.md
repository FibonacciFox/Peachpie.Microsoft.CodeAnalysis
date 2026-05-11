# Peachpie.Microsoft.CodeAnalysis

`Peachpie.Microsoft.CodeAnalysis` is the PeachPie-maintained Roslyn fork used by the PeachPie compiler.

This repository is based on modern `dotnet/roslyn` sources and keeps a deliberately small PeachPie-specific patch layer on top of them.

## What this repository is

- upstream base: `dotnet/roslyn`
- downstream consumer: `PeachPie`
- package identity: `Peachpie.Microsoft.CodeAnalysis`

Its purpose is to provide the Roslyn compiler codebase in the shape required by PeachPie.

## Current PeachPie-specific surface

The remaining differences from the current Roslyn base are:

- PeachPie package identity for `Microsoft.CodeAnalysis`
- PeachPie `InternalsVisibleTo` access
- compiler strong-name alignment required by PeachPie
- package metadata

## Repository layout

- `src/`: Roslyn compiler and tooling sources
- `eng/`: build infrastructure
- `artifacts/`: local build outputs

## Build

Example pack command for the PeachPie compiler package:

```powershell
dotnet pack src\Compilers\Core\Portable\Microsoft.CodeAnalysis.csproj -c Release -p:Version=5.8.0-dev -p:UseSharedCompilation=false
```

The package is produced under:

```text
artifacts\packages\Release\Shipping\
```

## PeachPie validation loop

The normal downstream validation flow is:

1. Pack `Peachpie.Microsoft.CodeAnalysis`.
2. Copy the produced `.nupkg` into `D:\peachpie\.nugs`.
3. Clear the cached `peachpie.microsoft.codeanalysis\5.8.0-dev` package from the global NuGet cache.
4. Run:

```powershell
dotnet test D:\peachpie\Peachpie.sln -nologo -p:UseSharedCompilation=false
```

Note: the first full test run can occasionally hit a transient file lock on `Peachpie.CodeAnalysis.dll`. An immediate rerun has been consistently green in the current validated state.
