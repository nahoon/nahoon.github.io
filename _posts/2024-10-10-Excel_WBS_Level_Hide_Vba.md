# 엑셀 VBA를 사용하여 WBS 레벨에 따라 행과 열을 숨기기

엑셀에서 WBS(작업 분해 구조, Work Breakdown Structure)를 사용하여 여러 작업 항목을 관리할 때, 상위 및 하위 레벨의 항목을 효율적으로 숨기거나 표시하는 기능이 필요할 수 있습니다. 이 글에서는 특정 레벨을 선택했을 때, 관련된 하위 항목들을 숨기고 상위 항목은 그대로 표시하는 VBA 코드를 소개합니다. 또한, 선택된 레벨에 따라 열도 자동으로 숨겨지도록 기능을 구현합니다.

## 주요 요구 사항

- **Level1**이 선택되면 **Level2~Level4**의 행과 열을 숨긴다.
- **Level2**가 선택되면 **Level3~Level4**의 행과 열을 숨기고, **Level1**은 항상 표시된다.
- **Level3**이 선택되면 **Level4**의 행과 열을 숨기고, **Level1**과 **Level2**는 표시된다.
- **Level4**가 선택되면 모든 레벨을 표시한다.

이 요구 사항을 만족하는 코드를 VBA로 구현해보겠습니다.

## 구현 절차

### 1. 드롭다운 메뉴 추가

엑셀에서 사용자가 선택한 레벨에 따라 행과 열을 숨기려면, 우선 드롭다운 메뉴를 추가해야 합니다. 다음 단계에 따라 드롭다운 메뉴를 설정합니다.

1. **드롭다운을 추가할 셀 선택**: 드롭다운을 추가하고 싶은 셀(예: **C4**)을 선택합니다.
2. **데이터 유효성 검사 설정**: 
    1. **데이터 탭**에서 `데이터 유효성 검사`를 클릭합니다.
    2. `설정` 탭에서 **유효성 기준**을 `목록`으로 선택합니다.
    3. **원본** 필드에 `Level1,Level2,Level3,Level4`를 입력합니다.
3. **드롭다운 확인**: 드롭다운 메뉴가 정상적으로 표시되는지 확인합니다. **C4** 셀을 클릭하면 **Level1**, **Level2**, **Level3**, **Level4** 중 하나를 선택할 수 있습니다.

이제 사용자가 선택한 레벨에 따라 VBA 코드가 동작하게 됩니다.

### 2. VBA 코드 작성

다음은 해당 기능을 구현한 최종 VBA 코드입니다.

\\\`\\\`\\\`
Private Sub Worksheet_Change(ByVal Target As Range)
    ' 상수 정의
    Const dropdownCell As String = "$C$4"  ' 드롭다운이 위치한 셀
    Const startRow As Long = 7             ' 데이터가 시작되는 첫 번째 행 번호
    Const level1Col As Long = 4            ' Level1이 위치한 열 (D열)
    Const level2Col As Long = 5            ' Level2이 위치한 열 (E열)
    Const level3Col As Long = 6            ' Level3이 위치한 열 (F열)
    Const level4Col As Long = 7            ' Level4이 위치한 열 (G열)
    
    ' 드롭다운이 있는 셀을 지정 (예: C4 셀)
    If Target.Address = dropdownCell Then
        Dim selectedLevel As String
        selectedLevel = Target.Value
        
        ' 모든 행 보이기
        Rows.Hidden = False
        
        ' Level1~Level4 열에서 마지막 값이 있는 행 번호 찾기
        Dim lastRowLevel1 As Long
        Dim lastRowLevel2 As Long
        Dim lastRowLevel3 As Long
        Dim lastRowLevel4 As Long
        Dim lastRow As Long
        
        ' 각 열의 마지막 값이 있는 행을 찾음
        lastRowLevel1 = Cells(Rows.Count, level1Col).End(xlUp).Row ' Level1
        lastRowLevel2 = Cells(Rows.Count, level2Col).End(xlUp).Row ' Level2
        lastRowLevel3 = Cells(Rows.Count, level3Col).End(xlUp).Row ' Level3
        lastRowLevel4 = Cells(Rows.Count, level4Col).End(xlUp).Row ' Level4
        
        ' 마지막 값이 있는 행 중 가장 큰 값을 마지막 행으로 설정
        lastRow = Application.WorksheetFunction.Max(lastRowLevel1, lastRowLevel2, lastRowLevel3, lastRowLevel4)
        
        ' 선택된 Level에 따라 상위 항목은 표시하고 하위 항목을 숨김
        For i = startRow To lastRow ' 데이터가 시작하는 행부터 마지막 행까지 반복
            Select Case selectedLevel
                Case "Level1"
                    ' Level1의 빈 값만 숨기기
                    If Cells(i, level1Col).Value = "" Then
                        Rows(i).Hidden = True
                    End If
                
                Case "Level2"
                    ' Level1은 항상 보이고, Level2의 빈 값 숨기기
                    If Cells(i, level1Col).Value <> "" Then
                        Rows(i).Hidden = False ' Level1에 값이 있으면 보이도록 설정
                    ElseIf Cells(i, level2Col).Value = "" Then
                        Rows(i).Hidden = True ' Level2의 빈 값만 숨김
                    End If
                
                Case "Level3"
                    ' Level1, Level2는 항상 보이고, Level3의 빈 값 숨기기
                    If Cells(i, level1Col).Value <> "" Then
                        Rows(i).Hidden = False ' Level1에 값이 있으면 보이도록 설정
                    ElseIf Cells(i, level2Col).Value <> "" Then
                        Rows(i).Hidden = False ' Level2에 값이 있으면 보이도록 설정
                    ElseIf Cells(i, level3Col).Value = "" Then
                        Rows(i).Hidden = True ' Level3의 빈 값만 숨김
                    End If
                
                Case "Level4"
                    ' 모든 레벨을 표시
                    ' 아무것도 숨기지 않음
            End Select
        Next i
        
        ' 모든 열 보이기
        Columns.Hidden = False
        
        ' 열 숨기기 기능 추가
        Select Case selectedLevel
            Case "Level1"
                ' Level2 ~ Level4 열 숨기기
                Columns(ColLetter(level2Col) & ":" & ColLetter(level4Col)).Hidden = True
                
            Case "Level2"
                ' Level3 ~ Level4 열 숨기기
                Columns(ColLetter(level3Col) & ":" & ColLetter(level4Col)).Hidden = True
                
            Case "Level3"
                ' Level4 열 숨기기
                Columns(ColLetter(level4Col)).Hidden = True
                
            Case "Level4"
                ' 모든 열 보이기
                ' 아무것도 숨기지 않음
        End Select
        
    End If
End Sub

' 열 번호를 열 문자로 변환하는 함수
Function ColLetter(ColNum As Long) As String
    ColLetter = Split(Cells(1, ColNum).Address, "$")(1)
End Function
\\\`\\\`\\\`

### 코드 설명

1. **상수 정의**: 드롭다운이 위치한 셀(`C4`), 데이터가 시작되는 행 번호(`7`), 각 레벨의 열 번호(`D열`~`G열`)를 상수로 정의하여 코드를 간결하고 유지보수하기 쉽게 만들었습니다.
   
2. **행 숨기기**: 
   - 각 레벨의 마지막 값이 있는 행 번호를 찾기 위해 `Cells(Rows.Count, 열 번호).End(xlUp).Row` 메서드를 사용했습니다. 
     - 예: `lastRowLevel1 = Cells(Rows.Count, level1Col).End(xlUp).Row`는 Level1 열(D열)에서 마지막 값을 가진 행 번호를 찾습니다.
   - 이 방식은 해당 열에서 가장 아래에 위치한 데이터를 찾아 그 행 번호를 반환합니다. 이후, 이 행 번호를 바탕으로 어떤 행까지 반복할지 결정합니다.
   
3. **행 숨기기 작업**: `For` 루프와 `Select Case` 문을 사용하여 선택한 레벨에 따라 각 항목의 행을 숨기거나 표시합니다. 예를 들어, **Level2**가 선택되면 **Level1**은 항상 표시되고, **Level2**의 빈 값이 있는 행은 숨깁니다.

4. **열 숨기기**: `Select Case` 문을 사용해 선택된 레벨에 따라 하위 레벨의 열을 숨깁니다. **Level1**을 선택하면 **Level2~Level4** 열이 숨겨지고, **Level3**이 선택되면 **Level4** 열만 숨겨집니다.

5. **열 문자 변환 함수**: `ColLetter` 함수를 통해 열 번호를 열 문자(D, E, F 등)로 변환하여 열 숨기기 기능에서 사용할 수 있도록 했습니다.

### 마무리

이 VBA 코드를 활용하면 WBS 작업에서 레벨별로 하위 항목을 효율적으로 숨기거나 표시할 수 있어 작업 관리가 훨씬 수월해집니다. 원하는 레벨을 선택함으로써 복잡한 작업 항목을 한눈에 확인하고, 필요한 경우 하위 항목들을 숨길 수 있습니다. 프로젝트 관리에 이 기능을 도입해보세요!