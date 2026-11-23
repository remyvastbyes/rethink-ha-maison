# rethink (anszom) avec le pilote lavante-séchante de la PR #308 (victormantuaneli)
# + alias pour le LG F852SV50WRS (modèle ThinQ F_V7_F___W.B_2QEUK).
FROM alpine:3.24 AS build
WORKDIR /app
RUN apk add --no-cache nodejs npm git
ARG RETHINK_REPO=https://github.com/victormantuaneli/rethink
ARG RETHINK_COMMIT=c57d56583a661af5422dcb652eeafcdf5fd3317e
RUN git init -q . && git remote add origin $RETHINK_REPO \
 && git fetch -q --depth 1 origin $RETHINK_COMMIT && git checkout -q FETCH_HEAD
RUN sed -i "/'F_V7_F___W.B__QEUK': F_V__F___W_B_1QEUK,/a\    'F_V7_F___W.B_2QEUK': F_V__F___W_B_1QEUK, // LG F852SV50WRS washer/dryer (alias)" cloud/ha_bridge.ts \
 && grep -q "F_V7_F___W.B_2QEUK" cloud/ha_bridge.ts
RUN npm ci --ignore-scripts
ARG GIT_REVISION=c57d565+maison
RUN npm run build && npm prune --omit=dev

FROM alpine:3.24
WORKDIR /app
RUN apk upgrade --no-cache && apk add --no-cache nodejs ca-certificates
COPY --from=build /app/package.json /app/package-lock.json ./
COPY --from=build /app/node_modules ./node_modules
COPY --from=build /app/dist ./dist
CMD ["node", "dist/rethink-cloud.js"]
