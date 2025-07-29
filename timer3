import streamlit as st
import time

# 사이트 이름 및 아이콘, 레이아웃 설정
st.set_page_config(
    page_title='위니브 타이머',
    page_icon='⏰',
    layout='centered'
)

st.markdown("""
<div style="text-align: center; margin: 2rem 0;">
    <h1 style="font-size: 3rem; font-weight: bold;">위니브 타이머</h1>
    <p style="color: #888; font-size: 0.8rem;">작업 리듬을 만들어주는 음악 타이머</p>
</div>
""", unsafe_allow_html=True)

# 세션 상태 초기화
defaults = {
    "timer_running": False,
    "timer_paused": False,
    "start_time": None,
    "total_pause_time": 0,
    "total_seconds": 25 * 60,
    "timer_completed": False,
    "show_celebration": False,
    "remaining_seconds": 25 * 60,
    "pause_start_time": None,
    "select_music": "없음",
    "music_auto_play": True
}
for key, val in defaults.items():
    if key not in st.session_state:
        st.session_state[key] = val


def update_timer():
    if st.session_state.timer_running and not st.session_state.timer_paused:
        current_time = time.time()
        elapsed = current_time - st.session_state.start_time - st.session_state.total_pause_time
        remaining = st.session_state.total_seconds - int(elapsed)

        if remaining <= 0:
            st.session_state.remaining_seconds = 0
            st.session_state.timer_running = False
            st.session_state.timer_completed = True
            st.session_state.show_celebration = True
        else:
            st.session_state.remaining_seconds = remaining


def get_timer_status():
    if st.session_state.timer_completed:
        return "completed"
    elif st.session_state.timer_running and not st.session_state.timer_paused:
        return "running"
    elif st.session_state.timer_paused:
        return "paused"
    else:
        return "stopped"


def format_time(second):
    hours = second // 3600
    minutes = (second % 3600) // 60
    seconds = (second % 60)
    return f"{hours:02d}:{minutes:02d}:{seconds:02d}"


def set_timer_duration(minutes):
    st.session_state.total_seconds = minutes * 60
    st.session_state.remaining_seconds = minutes * 60


def reset_timer():
    st.session_state.timer_running = False
    st.session_state.timer_paused = False
    st.session_state.start_time = None
    st.session_state.total_pause_time = 0
    st.session_state.timer_completed = False
    st.session_state.show_celebration = False
    st.session_state.pause_start_time = None


update_timer()
current_status = get_timer_status()

col_left, col_right = st.columns(2)

with col_left:
    st.markdown('<div class="timer-display">', unsafe_allow_html=True)
    if st.session_state.total_seconds > 0:
        progress = st.session_state.remaining_seconds / st.session_state.total_seconds
        progress = max(0, min(1, progress))
    else:
        progress = 0
    st.progress(float(progress))

    status_col1, _, status_col3 = st.columns(3)
    with status_col1:
        if current_status == "running":
            st.markdown('**타이머**', help="타이머가 실행중입니다.")
        elif current_status == "paused":
            st.markdown('**타이머**', help="타이머가 일시 정지 되었습니다.")
        elif current_status == "completed":
            st.markdown('**타이머**', help="타이머가 완료되었습니다!")
        else:
            st.markdown('**타이머**', help="타이머가 대기중입니다.")
    with status_col3:
        st.markdown(f'<p style="text-align:right;"><strong>{int(progress * 100)}%</strong></p>', unsafe_allow_html=True)

    timer_color = "#ff4444" if st.session_state.remaining_seconds <= 60 else "var(--primary-text-color)"
    st.markdown(f"""
    <div class="timer-time" style="text-align: center; color:{timer_color}; font-size: 4rem; font-weight: bold; margin: 2rem 0;">
        {format_time(st.session_state.remaining_seconds)}
    </div>
    """, unsafe_allow_html=True)

    if st.session_state.total_seconds > 0:
        col1, col2 = st.columns(2)
        with col1:
            st.metric("설정 시간", format_time(st.session_state.total_seconds))
        with col2:
            elapsed = st.session_state.total_seconds - st.session_state.remaining_seconds
            st.metric("경과 시간", format_time(elapsed))

    if st.session_state.timer_completed and st.session_state.show_celebration:
        st.balloons()
        st.success("타이머가 완료되었습니다. 목표 시간을 달성했습니다!")

    if st.session_state.timer_running and not st.session_state.timer_paused:
        if st.session_state.remaining_seconds <= 10 and st.session_state.remaining_seconds > 0:
            st.error("10초 이하 남았습니다")
        elif st.session_state.remaining_seconds <= 60:
            st.warning("1분 이하 남았습니다")

    btn1, btn2, btn3 = st.columns(3)
    with btn1:
        if not st.session_state.timer_running and not st.session_state.timer_paused:
            if st.button("▶️", help="시작", type='primary'):
                st.session_state.timer_running = True
                st.session_state.start_time = time.time()
                st.session_state.total_pause_time = 0
                st.session_state.timer_completed = False
                st.success("타이머가 시작되었습니다!")
                st.rerun()
        if st.session_state.timer_running and not st.session_state.timer_paused:
            if st.button("⏸️", help="일시정지", type='primary'):
                st.session_state.timer_paused = True
                st.session_state.pause_start_time = time.time()
                st.info("타이머가 일시정지 되었습니다.")
                st.rerun()
        elif st.session_state.timer_paused:
            if st.button("▶️", help="재개", type='primary'):
                st.session_state.timer_paused = False
                if st.session_state.pause_start_time:
                    pause_duration = time.time() - st.session_state.pause_start_time
                    st.session_state.total_pause_time += pause_duration
                    st.session_state.pause_start_time = None
                st.success("타이머가 재개되었습니다!")
                st.rerun()

    with btn2:
        if st.button("🔁", help="리셋"):
            reset_timer()
            st.session_state.total_seconds = 25 * 60
            st.session_state.remaining_seconds = 25 * 60
            st.info("타이머가 리셋되었습니다.")
            st.rerun()

    with btn3:
        if st.button("1분 추가", help="1분 추가하기"):
            st.session_state.remaining_seconds += 60
            st.session_state.total_seconds += 60
            st.session_state.timer_completed = False
            st.session_state.show_celebration = False
            st.toast("1분이 추가되었습니다!")
            st.rerun()

    st.markdown("**🎵 배경 음악**")
    background_music = {
        "없음": None,
        "Bubblegum Code-2": "./music/Bubblegum Code-2.mp3",
        "Bubblegum Code": "./music/Bubblegum Code.mp3",
        "Code in the Moonlight": "./music/Code in the Moonlight.mp3",
        "Gentle Streams": "./music/Gentle Streams.mp3",
        "Late Night Thoughts": "./music/Late Night Thoughts.mp3",
        "Soft Light Waves": "./music/Soft Light Waves.mp3"
    }

    select_music = st.selectbox(
        "음악을 선택하세요:",
        options=list(background_music.keys()),
        index=list(background_music.keys()).index(st.session_state.select_music),
        label_visibility="collapsed"
    )
    st.session_state.select_music = select_music
    if st.session_state.select_music != "없음":
        try:
            audio_file_path = background_music[st.session_state.select_music]
            st.audio(audio_file_path, format='audio/mpeg', loop=True, autoplay=st.session_state.music_auto_play)
        except:
            st.warning("음악 파일을 찾을 수 없습니다.")

    st.session_state.music_auto_play = st.toggle("음악 자동재생", value=st.session_state.music_auto_play)

with col_right:
    timer_presets = {
        "5분": 5, "15분": 15, "25분": 25,
        "30분": 30, "45분": 45, "60분": 60
    }
    st.markdown("**타이머 설정**")
    st.markdown("**빠른 타이머 설정**")

    p1, p2, p3 = st.columns(3)
    preset_button = [(p1, ["5분", "30분"]), (p2, ["15분", "45분"]), (p3, ["25분", "60분"])]

    for col, p in preset_button:
        with col:
            for preset in p:
                if st.button(preset, key=f"preset_{preset}"):
                    minutes = timer_presets[preset]
                    set_timer_duration(minutes)
                    reset_timer()
                    st.toast(f"{preset} 설정 완료!")
                    time.sleep(3)
                    st.rerun()

    st.divider()

    sc1, sc2 = st.columns([0.8, 0.2])
    slider_m = st.slider("타이머 시간", 1, 120, st.session_state.total_seconds // 60)
    with sc1:
        st.markdown("사용자 설정(분)")
    with sc2:
        st.markdown(f"<p style='text-align:right;'><strong>{slider_m}분</strong></p>", unsafe_allow_html=True)

    st.divider()

    if st.button("⚙️설정 적용", type='primary'):
        set_timer_duration(slider_m)
        reset_timer()
        st.toast(f"{slider_m}분 설정 완료!")
        time.sleep(1.2)
        st.rerun()

    st.markdown("⏰ 타이머 상세 설정(시:분:초)")
    col1, col2, col3 = st.columns(3)
    with col1:
        hour = st.number_input("시간", min_value=0, max_value=23, value=st.session_state.total_seconds // 3600)
    with col2:
        minute = st.number_input("분", min_value=0, max_value=59, value=(st.session_state.total_seconds % 3600 // 60))
    with col3:
        second = st.number_input("초", min_value=0, max_value=59, value=st.session_state.total_seconds % 60)

    if st.button("🔧 상세설정 적용"):
        st.session_state.total_seconds = hour * 3600 + minute * 60 + second
        st.session_state.remaining_seconds = st.session_state.total_seconds
        reset_timer()
        st.toast("상세 시간 설정 완료!")
        time.sleep(1.2)
        st.rerun()

# 자동 새로고침
if st.session_state.timer_running and not st.session_state.timer_paused and not st.session_state.timer_completed:
    time.sleep(1.2)
    st.rerun()

# 푸터
st.divider()
st.markdown("""
    <footer style='text-align: center; padding: 20px 0; color: gray; font-size: 0.9em;'>
        hkgorud all rights reserved
    </footer>
""", unsafe_allow_html=True)
