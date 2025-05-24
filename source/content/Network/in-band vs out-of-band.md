- In-band signaling is the sending of control information within the same band or channel.
    - [https://en.m.wikipedia.org/wiki/In-band_signaling](https://en.m.wikipedia.org/wiki/In-band_signaling)
- 네트워크 장비에서 물리적 링크를 통해 흐르는 트래픽은 사용자 데이터와 관리 차원의 컨트롤 데이터로 나누어짐. 기본적으로 In-Band 라고 하면 컨트롤 트래픽이 사용자 트래픽과 같이 흐르는 것을 말하며 Out-Of-Band는 컨트롤 트래픽을 사용자 채널에서 분리 시킨 것을 의미
- out-of-band를 사용하면 컨트롤 트래픽과 사용자 트래픽이 분리되어 안정적이나, 규모가 크면 out-of-band를 위한 인터페이스 및 네트워크를 모두 잡기가 번거롭고 비용이 많이 드므로 in-band가 많이 사용됨
- 스위치에서 여러가지 mgmt 관련 세팅을 할 때 out-of-band 환경에서는 mgmt interface를 따로 잡으므로 이에 유의하여 작업을 진행할 필요가 있음

![[Pasted image 20250524235024.png|600]]