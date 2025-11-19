import streamlit as st
import pandas as pd
import numpy as np
from PIL import Image
import matplotlib.pyplot as plt
import io

# 상품 데이터 로드
@st.cache
def load_products():
    return pd.read_csv('products.csv')

# 미션별 예산 설정
MISSION_BUDGET = {
    '카레 만들기': 15000,
    '여름캠핑 준비하기': 30000,
    '친구 생일파티 준비하기': 20000
}

# 시작화면: 미션 선택
def start_page():
    st.title("장보기 미션 앱")
    st.subheader("미션을 선택해주세요!")
    mission = st.selectbox("미션 선택", ["카레 만들기", "여름캠핑 준비하기", "친구 생일파티 준비하기"])
    st.session_state.mission = mission
    st.session_state.budget = MISSION_BUDGET[mission]
    st.session_state.cart = []  # 장바구니 초기화
    st.session_state.total_price = 0  # 총 가격 초기화
    st.session_state.selected_items = []  # 선택된 상품 목록 초기화

    if st.button("미션 시작"):
        shopping_page()

# 쇼핑화면: 상품 목록과 장바구니
def shopping_page():
    st.title(f"{st.session_state.mission} 쇼핑")
    st.subheader(f"예산: {st.session_state.budget} 원")
    
    products = load_products()
    col1, col2, col3 = st.columns([1, 3, 1])

    with col2:
        for _, product in products.iterrows():
            st.image(product['이미지url'], width=100)
            st.write(f"**{product['품명']}** - {product['가격']} 원")
            quantity = st.number_input(f"{product['품명']} 수량", min_value=0, max_value=10, key=product['품명'])
            
            if quantity > 0:
                if product['품명'] not in [item['품명'] for item in st.session_state.cart]:
                    st.session_state.cart.append({"품명": product['품명'], "가격": product['가격'], "수량": quantity})
                else:
                    for item in st.session_state.cart:
                        if item['품명'] == product['품명']:
                            item['수량'] = quantity

    # 장바구니
    st.subheader("장바구니")
    total = 0
    for item in st.session_state.cart:
        total += item['가격'] * item['수량']
        st.write(f"{item['품명']} - 수량: {item['수량']} - 가격: {item['가격']} 원")

    st.write(f"총 금액: {total} 원")

    if total > st.session_state.budget:
        st.warning("예산을 초과했습니다! 예산 내에서만 쇼핑을 진행해주세요.")
        st.button("제출하기", disabled=True)
    else:
        if st.button("제출하기"):
            result_page()

# 결과화면: 구매한 물건 목록과 이유 작성
def result_page():
    st.title("결과 화면")
    st.subheader(f"{st.session_state.mission} 미션 완료!")

    total = 0
    for item in st.session_state.cart:
        total += item['가격'] * item['수량']
        st.image(item['품명'], width=100)
        st.write(f"{item['품명']} - 수량: {item['수량']} - 가격: {item['가격']} 원")

    remaining = st.session_state.budget - total
    st.write(f"총 금액: {total} 원")
    st.write(f"남은 금액: {remaining} 원")

    purchase_reason = st.text_area("구매 이유를 작성해주세요.")

    if purchase_reason:
        st.button("그림으로 저장", on_click=save_image, args=(purchase_reason,))

# 이미지로 저장 기능
def save_image(purchase_reason):
    # 결과 화면을 이미지로 저장
    fig, ax = plt.subplots(figsize=(8, 6))
    ax.text(0.5, 0.9, f"미션: {st.session_state.mission}", fontsize=14, ha='center', va='center')
    ax.text(0.5, 0.85, f"총 금액: {st.session_state.budget - (st.session_state.budget - st.session_state.total_price)} 원", fontsize=12, ha='center', va='center')
    ax.text(0.5, 0.8, f"구매 이유: {purchase_reason}", fontsize=12, ha='center', va='center')

    # 상품 목록 그리기
    y_offset = 0.7
    for item in st.session_state.cart:
        ax.text(0.1, y_offset, f"{item['품명']} - {item['수량']} 개", fontsize=10)
        y_offset -= 0.05

    ax.axis('off')

    # 이미지를 바이트로 변환하여 다운로드 링크 생성
    buf = io.BytesIO()
    plt.savefig(buf, format='png')
    buf.seek(0)
    st.download_button("이미지 저장", buf, file_name="purchase_result.png", mime="image/png")
    plt.close(fig)

# 앱 실행
if __name__ == "__main__":
    if 'mission' not in st.session_state:
        start_page()
    else:
        shopping_page()
