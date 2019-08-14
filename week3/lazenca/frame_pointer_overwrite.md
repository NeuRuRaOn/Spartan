# week3  
## lazenca  
### frame pointer overwirte(x86)  
  * Frame Pointer를 1byte씩 덮어써서 코드의 흐름을 변경 가능  
  * 32bit Binary의 경우 stack을 16byte 경계에 정렬하는 코드가 추가됨  
  * x86-64 ABI는 16 byte 스택 정렬이 필요(호환되지 않는 환경에서의 스텍 공간을 제한하여 사용하기 위해서)  
  * 팬티엄III에서 SSE(Streaming Simd Extension) 데이터 유형_m128이 16 바이트 정렬되지 않을 시 올바르게 작동 안 할 수 있음  
### Stack alignment at 16-byte boundary(16바이트 경계에 스택을 정렬하기 위한 코드의 동작 순서)(x86)
  * main() 함수가 시작되는 부분에서 이전 함수에서 사용하던 frame pointer를 stack에 저장하기 전에 stack alignment를 진행  
  * main() 함수가 종료되는 부분에서는 leave 명령어를 실행하기 전 ebp 레지스터에 저장된 주소에 0x4를 뺀 영역에 저장된 값을 ecx 레지스터에 저장  
  * leave 명령어 실행 후 ecx 레지스터에 저장된 주소에 0x4를 뺀 주소 값을 esp 레지스터에 저장  
  * stack alignment 코드가 없는 경우엔 esp 레지스터의 값이 leave 코드에 의해 변경됨  
  * stack alignment 코드 적용 시 ret 코드가 실행되기 전에 "lea esp,[ecx-0x4]" 코드에 의해 esp 값이 변경됨  
  * 하지만 ecx 값은 leave 코드가 실행되기 전 ebp를 통해 값을 저장하기에 esp 레지스터 값 변경 가능  
### LEAVE, LEAVE instruction(x64)  
  * vuln() 함수에서 overflow로 인해 main() 함수의 frame pointer를 1바이트씩 변경 가능  
  * vuln() 함수 종료 시 변경된 frame pointer는 leave 명령어 때문에 rbp 레지스터에 저장  
  * 위의 과정들의 영향으로 ret 명령어에 의해 이동할 영역 변경 가능  
    * 공격 형태는 frame faking과 동일  
    * 단 frame faking은 return address 영역까지 overflow하는 반면, fpo는 frame pointer를 1바이트씩 overwrite함  

