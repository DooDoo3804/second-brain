---
title: 파이썬으로 프로그램 만들기
tags:
  - 파이썬
---
## Pyinstaller?

[Pyinstaller](https://pyinstaller.org/en/stable/)를 사용하면 파이썬으로 제작한 프로그램을 간단하게 실행 파일로 만들 수 있다. 실행 파일에 파이썬도 포함되어 있기 때문에 파이썬이 설치되지 않은 pc에서도 실행이 가능하다. 쉘에 아래 명령어를 통해 설치할 수 있다.

## Pyinstaller 사용하기


```bash
pip install pyinstaller
```

프로그램으로 만들어 실행할 파이썬 파일을 작성한다. 프로그램을 하나의 파일로 배포하려면 `--onefile` 옵션을 추가하면 된다. 아래 예시 코드를 준비했다. csv 파일을 읽어 'discharge'와 'charge'의 마지막 값을 따로 저장하는 코드이다. 'pandas' 라이브러리를 사용해 작성했다.

```python title="exportCSV_V2.py"
import os
import sys
import glob
import pandas as pd
import datetime

directory = os.path.dirname(sys.executable)
excel_files = glob.glob(os.path.join(directory, '*.csv'))
if (excel_files == []):
    print("파일 없음")
os.makedirs(directory + '/Results_V2', exist_ok=True)
filename = datetime.datetime.now().strftime('%Y-%m-%d-%H-%M-%S')
charge_list = []
discharge_list = []
for file in excel_files:
    df = pd.read_csv(file)
    for i in range(0, len(df.columns) - 1, 3):
        cycle = (i // 6) + 1
        subset = df.iloc[:, i:i+3]
        last_valid_index = subset.last_valid_index()
        tail_value = subset.loc[last_valid_index]
        if (i % 2) :
            discharge_list.append([cycle, "DisCharge", tail_value[0]])
        else :
            charge_list.append([cycle, "Charge", tail_value[0]])
charge_value_df = pd.DataFrame(charge_list, columns=['Cycle', 'Mode', 'Capacity'])
discharge_value_df = pd.DataFrame(discharge_list, columns=['Cycle', 'Mode', 'Capacity'])
charge_value_df = charge_value_df.sort_values('Cycle')
discharge_value_df = discharge_value_df.sort_values('Cycle')
charge_value_df.to_csv(directory + '/Results_V2/' + filename + '_ChargeResult.csv', index=False, encoding='cp949')
discharge_value_df.to_csv(directory + '/Results_V2/' + filename + '_DisChargeResult.csv', index=False, encoding='cp949')
```

pandas를 사용해서 코드를 작성했지만 pyinstaller에 pandas 라이브러리가 추가되지 않았다. 이런 경우에는 `--hidden-import={추가할 라이브러리}` 옵션으로 번들에 라이브러리를 추가한다.

```bash
pyinstaller --onefile --hidden-import=pandas exportCSV_V2.py
```

![[Pasted image 20231201193521.png]]

작성한 파이썬 파일 외에 spec(옵션 파일)과, dist, build 폴더가 생성된다. 최종 프로그램은 dist 폴더 안에 파일 하나로 통합되어 있다.