# 🎮 Unity 공부 기록 (2025-03-03)

# 🔥 문제: BGM과 SFX 볼륨을 개별적으로 조절하려 했으나, BGM만 적용되고 SFX는 정상적으로 조절되지 않음  

## 🛑 발생한 문제:  
- AudioMixer를 사용하여 BGM과 SFX의 볼륨을 개별적으로 조절하려 했으나, BGM만 정상적으로 적용되고 SFX는 변경되지 않는 문제 발생
- SettingPopup에서 AudioMixer.SetFloat()을 호출했지만, SFX 값이 변경되지 않음
## 🚀 원인 분석  
### 1️⃣ AudioMixer에 SFX 파라미터가 제대로 설정되지 않음
- Unity의 AudioMixer에서 BGM과 SFX를 개별적으로 제어하려면, 각각의 Expose to script(스크립트에서 사용 가능하도록 노출) 설정이 필요
- 하지만 SFX 볼륨을 조절하는 파라미터가 AudioMixer에서 설정되지 않았거나, 잘못된 이름을 사용했을 가능성이 큼
### 2️⃣ AudioMixer 그룹이 올바르게 연결되지 않음  
- Unity에서 AudioSource는 특정 AudioMixerGroup에 연결되어야 볼륨 조절이 적용됨
- BGM과 SFX가 같은 AudioMixerGroup에 속해 있다면, 볼륨 조절이 개별적으로 적용되지 않을 수 있음
- SFX AudioSource가 올바른 AudioMixerGroup에 할당되지 않았을 가능성이 있음
### 3️⃣ AudioMixer.SetFloat()에 잘못된 파라미터를 전달했을 가능성  
- AudioMixer.SetFloat()를 호출할 때 올바른 파라미터 이름을 사용해야 함
- Mixer.SFX의 이름이 AudioMixer 내에서 다르게 설정되었거나, null이 반환될 경우 값이 조절되지 않음

---
# ✅ 해결 방법

## 🎯 1. AudioMixer에서 올바른 파라미터 설정 확인  
- Unity의 AudioMixer 창에서 BGM과 SFX를 개별적으로 조절하는 파라미터가 노출(Expose to script)되어 있는지 확인
[설정 방법]  
- AudioMixer 창 열기 (Window → Audio → Audio Mixer)
- BGM과 SFX 각각에 해당하는 AudioMixerGroup을 선택
- "Volume" 노브 우클릭 → Expose to script 선택
- Inspector에서 Exposed Parameters 패널 확인 (Ctrl + 6 → Audio Mixer 설정)
- BGMVolume 및 SFXVolume 이름이 정확한지 확인
- 이름이 다르면 Mixer.SFX와 Mixer.BGM에서 올바르게 참조하도록 수정

---
## 🎯 2. AudioMixerGroup이 올바르게 연결되었는지 확인  
- Unity에서 각 AudioSource의 Output이 올바른 AudioMixerGroup에 설정되었는지 확인  
[설정 방법]
- BGM을 재생하는 AudioSource가 BGM_MixerGroup에 연결되어 있는지 확인
- SFX를 재생하는 AudioSource가 SFX_MixerGroup에 연결되어 있는지 확인
- SFXSource가 AudioMixerGroup에 연결되지 않으면 볼륨 조절이 적용되지 않음
## 🎯 3. AudioMixer.SetFloat() 호출 시 정확한 파라미터 이름 사용  
- AudioMixer에서 노출한 파라미터 이름(Exposed Parameters)이 올바른지 확인 후, 스크립트에서 정확히 호출해야 함
- SettingPopup.cs에서 AudioMixer.SetFloat() 수정

---
# 📌 오늘의 회고
- AudioMixer에서 개별적으로 조절하려면 Expose to script 설정이 필수이다.
- AudioSource가 올바른 AudioMixerGroup에 연결되지 않으면 볼륨 조절이 작동하지 않는다.
- AudioMixer.SetFloat()를 호출할 때 노출된 파라미터 이름이 정확해야 한다.

# 🎯 다음 목표:
- AudioMixer의 Mute(음소거) 기능 추가  
- PlayerPrefs를 활용하여 볼륨 설정 저장 및 로드 기능 구현  
