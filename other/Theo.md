#### Hot Module Replacement (Webpack Primitive)
https://youtu.be/iNvNYt53oOI?t=3500
Theo Ping.gg dev workflow:
1 get in call
2 init av devices
3 connect webrtc provider
4 connect websocket provider(s)
5 activate devices and confirm in correct state
6 join call

- save and full forced refresh; runs next set of page primitives eg fetch av devices before cleanup finished before? then end up in chaotic state everytime i save a file
- hot module replacement (a webpack primitive) is not implemented by remix; theo's presence state (eg mic is on) is interrupted by full page refresh post save because [volatile presence state] is maintained somewhere other than on a server

