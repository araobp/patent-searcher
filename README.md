# 自学自習

特許調査会社に所属せず、自学自習で特許検索スキルを向上中。

## 青本

最初は何が何だか分からなかったが、この本を熟読することで、特許検索スキルが向上しつつある。

## 母集団作成スキル

網を張るスキル。構成要素分解、最適な分類。自分が関心ある分野の代表的なIPC/FI（メイングループまで）やテーマコードは暗記するように。

## Copilot

演習用の問題を生成させたり、途中の検索プロセスを評価させたり。職場の先輩といった感じ。

## J-PlatPatとPatentSQUARE

適材適所で使い分け。

## IPランドスケープ

PatentSQUAREより特許データをダウンロードしCopilotやClaude Coworkへ。

## 技術スキル維持

Anitgravityへ電子工作や3D制作向けコード書かせて実機で動作させる。これが一番。

## Geminiが生成したこれって動く？J-PlatPat論理式をPatentSQUARE向けに変換。後で試験してみる。

動機：後々を考えると、J-PlatPatの論理式文法を覚えたい。しかし、PatentSQUAREで作業したい。


```python
import re

def jplatpat_to_patentsquare_perfect(j_query):
    """
    J-PlatPatのあらゆる構文を網羅してPatentSQUARE用に変換する完全版関数
    """
    converted = j_query
    
    # 1. 近傍検索（語順なし: N）の変換: ,10N, -> N10
    converted = re.sub(r',(\d+)N,', r' N\1 ', converted)
    
    # 2. 近傍検索（語順あり: E）の変換: ,10E, -> W10 (PatentSQUAREの語順固定演算子)
    # ※PatentSQUAREのバージョンや仕様によっては PRE/10 などの場合もあるので要調整
    converted = re.sub(r',(\d+)E,', r' W\1 ', converted)
    
    # 3. 特許分類（FI）のブラケット除去: [G06F17/30/FI] -> G06F17/30
    converted = re.sub(r'\[([^\]]+)/FI\]', r'\1', converted)
    
    # 4. 特許分類（Fターム）の変換: [5B075UU01/FT] -> 5B075UU01.FT.
    # テキスト検索窓にそのまま流し込める形式、またはブラケット除去
    converted = re.sub(r'\[([^\]]+)/FT\]', r'\1.FT.', converted)
    
    # 5. 項目タグの変換: /AB や /PA,APP などのスラッシュ以降をドット囲みに変換
    # スラッシュの後に続く英大文字やカンマの塊をキャッチ
    converted = re.sub(r'/([A-Z,]+)', r'.\1.', converted)
    
    # 6. 微調整（連続スペースの削除、ドットの整形など）
    converted = re.sub(r'\s+', ' ', converted).strip()
    
    return converted

# --- 網羅性のテスト ---
if __name__ == "__main__":
    # あらゆる複雑なパターンを詰め込んだテスト式
    j_input = "([5B075UU01/FT] * トヨタ/PA) + (自動運転 ,5E, 制御)/CL + [G06F17/30/FI]"
    
    print("--- 変換前（J-PlatPat） ---")
    print(j_input)
    
    ps_output = jplatpat_to_patentsquare_perfect(j_input)
    
    print("\n--- 変換後（PatentSQUARE完全版） ---")
    print(ps_output)
    # 期待される出力: (5B075UU01.FT. * トヨタ.PA.) + (自動運転 W5 制御).CL. + G06F17/30

```
