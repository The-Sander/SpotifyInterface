<template>
  <Home />
</template>

<script>
import Home from "./pages/Home.vue";
import { getTokenFromUrl, spotify } from "./assets/spotify";
import useUser, {
  setToken,
  setUser,
  setPlaylists,
  setActivePlaylist,
  setCurrentTrack,
  setPlaybackState,
  setIsSaved,
  setTop,
  setDevices,
} from "./modules/users";

export default {
  components: {
    Home,
  },
  created() {
    const hash = getTokenFromUrl();
    const { token } = useUser();
    window.location.hash = "";
    const _token = hash?.access_token;
    if (_token) {
      setToken(_token);
    }
    spotify.setAccessToken(_token || token.value);
    spotify.getMe().then((user) => {
      setUser(user);
    });
    spotify.getMyTopTracks().then((response) => {
      setTop(response.items);
    });
    spotify.getUserPlaylists({ limit: 50 }).then((playlists) => {
      setPlaylists(playlists);
      spotify.getPlaylist(playlists?.items[0]?.id).then((response) => {
        setActivePlaylist(response);
      });
    });
    spotify.getMyCurrentPlaybackState().then((res) => {
      setPlaybackState(res);
      setCurrentTrack(res.item);
      const tracks = [res.item?.id];
      spotify.containsMySavedTracks(tracks).then((res) => {
        setIsSaved(res);
      });
    });
    spotify.getMyDevices().then((res) => {
      setDevices(res);
    });
  },
};
</script>

<style></style>
