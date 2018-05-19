```
#include <IE.au3>

$Url = 'http://www.autoitscript.com/forum/index.php?app=core&module=global&section=login'
$User = 'user'
$Pwd = 'pwd'

$test = _IEAutoLogin($Url, $User, $Pwd)

Func _IEAutoLogin($sUrl, $sUsername, $sPwd)
 ;funkey 09.09.09
 $oIE = _IECreate($sUrl)
 _IEErrorNotify(False)
 _IEErrorHandlerRegister()
 $oForms = _IEFormGetCollection($oIE)
 If @error Then
  Return SetError(1, _IEErrorHandlerDeRegister(), $oIE) ;'no forms --> no login'
 Else
  $Index = 0
  For $oForm In $oForms
   $oFormElements = _IEFormElementGetCollection($oForm)
   If IsObj($oFormElements) Then
    $IndexElement = 0
    For $oElement In $oFormElements
     If $oElement.Type = 'password' Then
      $oPwd = _IEFormElementGetObjByName($oForm, $oElement.Name)
      _IEFormElementSetValue($oPwd, $sPwd)
      For $i = $IndexElement - 1 To 0 Step -1
       $oUser = _IEFormElementGetCollection($oForm, $i)
       If $oUser.Type = 'text' Then
        _IEFormElementSetValue($oUser, $sUsername)
        ExitLoop
       EndIf
      Next
      If $oForm.action <> "0" And Not StringInStr($oForm.action, '.php') Then  ;submit
       _IEFormSubmit($oForm, 0)
       _IELoadWait($oIE)
      Else  ;click
       For $i = $IndexElement + 1 To $IndexElement + 10 ;check the next 10 elements
        $oButton = _IEFormElementGetCollection($oForm, $i)
        If $oButton.Type = 'submit' Then
         _IEAction($oButton, "click")
         ExitLoop
        EndIf
       Next
      EndIf
      Return SetExtended(_IEErrorHandlerDeRegister(), $oIE)
     EndIf
     $IndexElement += 1
    Next
   EndIf
   $Index += 1
  Next
 EndIf
 Return SetError(2, _IEErrorHandlerDeRegister(), $oIE) ;no password-field found
EndFunc   ;==>_IEAutoLogin
'''

(c) s t i l g a r http://www.autoitscript.com/forum/index.php?showtopic=102133
