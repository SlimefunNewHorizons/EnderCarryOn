# Carried entity snapshots — candidate fix (#884)

Status: **unvalidated candidate**, not deployed. No production inventories or worlds were modified.

Large entity snapshots no longer need to occupy an NBT string limited to 65,535 modified UTF-8 bytes: the new codec retains small legacy strings and stores oversized snapshots as UTF-8 byte arrays under the existing key. The reader accepts both formats and rejects malformed bytes without changing the carrier. Release keeps the item when decoding or spawning fails, including a returned entity whose spawn was rejected. Existing listeners are preserved.

This source change is mirrored in Drakes-Suites, whose new regression tests cover encoding boundaries, legacy reads, malformed input, and preservation after restore failures. **Tests and live staging validation remain pending.** Two environment failures stopped verification: missing reactor dependencies, then an Adventure `ObjectContentsLike` class absent from the focused compilation classpath. No standalone Gradle build or supported Minecraft-version claim is made.

Before integration: build against the intended Paper APIs, run the regressions, and test large synthetic entities, packet encoding, persistence and spawn cancellation in an isolated server. Existing oversized legacy carriers are not rewritten automatically.

Before deployment, discarding the candidate branch is sufficient rollback. Once binary carriers exist, the old string-only reader cannot release them; keep a dual-format reader available and establish a compatible rollback with verified backups before deploying.

The existing snapshot format is experimental; this change does not promise migration between Minecraft versions. See the [Paper EntitySnapshot API](https://jd.papermc.io/paper/1.21.11/org/bukkit/entity/EntitySnapshot.html).
